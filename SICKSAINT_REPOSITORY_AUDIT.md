# SICKSAINT repository and integration audit

Updated 2026-11-23 for the staged package. The original supplied checkout at `C:\Users\ahsan\Desktop\SICKSAINT` contained the Next.js application and a separate Node/Prisma database directory, but no Laravel `artisan`, Composer manifest, Laravel routes, models, controllers, or middleware. Its prior Firebase files were setup scaffolding and Firebase was not the live app data layer.

After the user authorized proceeding, this independent staged package added a fresh Laravel 12 API in `backend/`. Because the original backend was absent, this is a new scaffold rather than an audit or preservation of unknown existing business logic, schema, or roles. No files in the original checkout were changed by this staging work.

## Target architecture

- One repository with `frontend/`, `backend/`, `docs/`, and `deployment/`.
- Next.js public site and Admin Panel deploy together from `frontend/` to Vercel.
- Laravel API deploys separately from `backend/` to PHP-compatible hosting.
- Laravel owns application authentication, users, roles, authorization, and API behavior. Firestore is the single application datastore accessed by Laravel using server-side IAM credentials. Next.js server routes proxy browser requests to Laravel.
- Firebase Authentication is not used; adding it would duplicate Laravel identity. Firestore Security Rules deny direct browser access.
- WordPress is the editorial CMS for published content. Public reads use the frontend WordPress REST adapter; editorial submissions are written only when backend publishing credentials are configured.

## Security and deployment boundaries

Local `.env`, local databases, dependency directories, and generated builds are excluded from the deployment ZIP. The `.env.example` files contain placeholders only. The original `.env` was not read or printed. No Firestore writes, DNS changes, or deployments were performed.

## Evidence and remaining items

See [FINAL_QA_REPORT.md](FINAL_QA_REPORT.md), [ROLE_PERMISSION_MATRIX.md](ROLE_PERMISSION_MATRIX.md), [FIREBASE_SETUP.md](FIREBASE_SETUP.md), [WORDPRESS_SETUP.md](WORDPRESS_SETUP.md), and [DEPLOYMENT_GUIDE.md](DEPLOYMENT_GUIDE.md). A service-account secret, live staging Firestore project, CMS, email provider, platform account, and complete role-by-role integration run remain prerequisites for release.
