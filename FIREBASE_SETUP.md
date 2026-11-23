# Firestore setup

Firestore is the single application datastore. Laravel remains the only application API and owns login, roles, permission checks, submissions, newsletter data, and all writes. The browser does not connect directly to Firestore and Firebase Authentication is not enabled; passwords and short-lived bearer tokens are managed by Laravel and stored as hashes in Firestore.

The Firebase web config shared for project `sicksaint` identifies the public project. The web API key is not a server credential and is intentionally not copied into the frontend. Laravel uses a Google service account credential with the Cloud Datastore User role to call Firestore over Google's REST API. No credential is included in the ZIP.

## Configure the Firebase project

1. In Firebase Console, confirm project `sicksaint` has Cloud Firestore enabled in Native mode. Choose the database location carefully before creating production data; location is not changed by this package.
2. Create a dedicated service account or use workload identity if the PHP host supports it. Grant only `roles/datastore.user` for this project.
3. Store its JSON credential as a server-only backend secret named `FIREBASE_CREDENTIALS_JSON`, or mount it as a file and set `GOOGLE_APPLICATION_CREDENTIALS` to the file path. Set `FIREBASE_PROJECT_ID=sicksaint`. Never commit the JSON key or put it in any `NEXT_PUBLIC_*` value.
4. The included `firestore.rules` denies direct client reads and writes. Deploy the rules and indexes from the repository root only after checking the selected Firebase project: `firebase deploy --project sicksaint --only firestore:rules,firestore:indexes`.
5. After setting backend credentials, run `php artisan sicksaint:firestore-check` from `backend/` to verify IAM/network access. It performs a read-only check and writes no data.

## Collections

- `users/{sha256-email}` stores user profile, password hash, role, verification time, and timestamps.
- `usernameIndex/{sha256-username}` reserves unique usernames and maps them to user IDs.
- `accessTokens/{sha256-token}` stores token owner and expiry; only the raw token is held in the HTTP-only frontend cookie.
- `passwordResets/{sha256-email}` stores a single-use reset-token hash and expiry.
- `submissions/{uuid}`, `reviews/{uuid}`, and `notifications/{uuid}` store editorial workflow records.
- `newsletterSubscribers/{sha256-email}` stores newsletter consent records.

Composite indexes for review history, notification filters, access-token invalidation, and newsletter export are in `firestore.indexes.json`. The backend requires network egress to `firestore.googleapis.com` and `oauth2.googleapis.com`; no gRPC extension is required.

## Local development and tests

Configure the backend variables in `backend/.env`; do not place the service account secret in frontend env files. The backend automated tests replace Firestore with an in-memory test adapter, so they do not create or modify any live Firebase data. To validate against a project, use a separate staging Firebase project and a least-privilege service account.

## Known limits

This implementation uses Firestore REST operations. Multi-document uniqueness is protected through deterministic email documents and username index documents, but the API does not yet wrap every cross-collection workflow in a Firestore transaction. Admin counts and list pagination currently cap each collection query at 1,000 records; add cursor pagination and aggregation before the app grows beyond that scale. Do not import production users or submissions until the migration and backup plan has been reviewed.
