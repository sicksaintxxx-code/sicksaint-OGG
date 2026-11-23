# SICKSAINT

Monorepo for the Next.js public magazine and admin panel, plus the Laravel API. The frontend and backend deploy independently from this repository.

## Layout

- `frontend/` — Next.js website, admin panel, and server-side BFF routes.
- `backend/` — Laravel 12 API, Firestore-backed authentication/roles/submissions/newsletter, and WordPress publishing adapter.
- `docs/` and `deployment/` — architecture and deployment notes.

The original supplied checkout contained the Next.js application but no Laravel backend. After your approval to proceed, this staged package adds a Laravel API scaffold. It does not claim to preserve an absent backend implementation.

## Local development

Requirements: Node.js 20+, PHP 8.2+, Composer, and a Firestore project plus server credential for app usage.

1. Backend: `cd backend`; install with `composer install`; copy `.env.example` to `.env`; run `php artisan key:generate`; configure `FIREBASE_PROJECT_ID` and server-only Firestore credentials (a mounted `GOOGLE_APPLICATION_CREDENTIALS` file, `FIREBASE_CREDENTIALS_JSON` secret, or local Google ADC); verify with `php artisan sicksaint:firestore-check`; start `php artisan serve --port=8000`.
2. Create the first administrator interactively with `php artisan sicksaint:make-admin`. The command writes to Firestore, prompts for a password, and does not print it.
3. Frontend: `cd frontend`; run `npm ci`; copy `.env.example` to `.env.local`; set `LARAVEL_API_URL=http://127.0.0.1:8000`; set `NEXT_PUBLIC_SITE_URL=http://localhost:3000`; run `npm run dev`.
4. Open `http://localhost:3000`.

The backend issues bearer tokens and stores only their hashes in Firestore; the Next.js server holds the raw token in an HTTP-only cookie. Browser calls use Next.js proxy routes; Laravel remains the authority for identity and roles. Configure a real mail transport before relying on reset or verification messages. Configure a WordPress Application Password to enable editorial publishing.

## Deployment

See [deployment/README.md](deployment/README.md) and [DEPLOYMENT_GUIDE.md](DEPLOYMENT_GUIDE.md). Vercel root directory is `frontend/`; the PHP host root is `backend/`. Set each target's environment separately. No deployment or DNS change has been performed.

Firestore is the app datastore; Firebase Authentication is not integrated. WordPress read integration is used for published editorial content; the Laravel publishing adapter requires WordPress credentials and has not been verified against a live CMS. See [FIREBASE_SETUP.md](FIREBASE_SETUP.md), [WORDPRESS_SETUP.md](WORDPRESS_SETUP.md), and [FINAL_QA_REPORT.md](FINAL_QA_REPORT.md).
