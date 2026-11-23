# Deployment guide

This is a two-target monorepo. Deployments are intentionally not run as part of package preparation.

## Vercel frontend

1. Import the shared GitHub repository in Vercel.
2. Set **Root Directory** to `frontend/` and framework to Next.js. Build command: `npm run build`.
3. Add a production environment variable `LARAVEL_API_URL` pointing to the HTTPS Laravel API origin, without a trailing path. Add `NEXT_PUBLIC_SITE_URL` with the public site origin.
4. Add `WORDPRESS_SITE_URL` and, if needed, `WORDPRESS_API_URL` for public article reads. Set `WORDPRESS_WEBHOOK_SECRET` to a long random value matching the CMS webhook configuration.
5. Deploy a Preview first. Verify content pages, media, authentication, session cookies, role protections, and submission/review flows against a staging backend.

Do not put Laravel tokens, WordPress Application Passwords, or other secrets in `NEXT_PUBLIC_*` variables.

## Laravel PHP host

1. Create a separate PHP-compatible service from the same repository and set its root directory to `backend/`.
2. Use PHP 8.2+ with Composer, OpenSSL, and outbound HTTPS access to Google OAuth/Firestore APIs. No gRPC extension or SQL database is used for app data.
3. Set `APP_ENV=production`, `APP_DEBUG=false`, a unique generated `APP_KEY`, `APP_URL` to the API origin, and `FRONTEND_URL` to the Vercel/custom frontend origin. Configure file/array cache, stateless API session behavior, synchronous or host-managed queue, and SMTP mail transport.
4. Set `FIREBASE_PROJECT_ID=sicksaint` and configure `FIREBASE_CREDENTIALS_JSON` as a protected backend secret, or mount a service-account file and set `GOOGLE_APPLICATION_CREDENTIALS`. Grant the service account `roles/datastore.user` only. Never set Firebase service credentials in frontend/Vercel public variables.
5. Verify connectivity with `php artisan sicksaint:firestore-check`. Then create an administrator using `php artisan sicksaint:make-admin`. Avoid assigning elevated roles through public registration.
6. Configure WordPress publishing with a dedicated CMS account and Application Password only after staging review. Use `WORDPRESS_SITE_URL`, `WORDPRESS_API_URL`, `WORDPRESS_USERNAME`, and `WORDPRESS_APPLICATION_PASSWORD` as backend-only environment variables.

## Release gates

- Provide Firestore IAM credentials, SMTP, WordPress, and host environment settings; no service credential is included in the ZIP.
- Confirm the selected Firestore database and location, deploy `firestore.rules` plus indexes to a staging Firebase project, and test account creation, login, password reset, role changes, submissions, notifications, and newsletter writes there.
- Test reader, contributor, editor, and admin authorization with separate accounts.
- Verify WordPress approval and publish operations on a staging CMS.
- Verify reset and verification emails with configured SMTP.
- Review PHP/Laravel runtime support: this scaffold is Laravel 12 and requires PHP 8.2+. Plan a Laravel 13 upgrade on a PHP 8.3+ runtime before Laravel 12 security fixes end in February 2027.
- Add the custom domain and DNS only after the user chooses a host and verifies staging. No domain or DNS action has been taken.

Firestore stores application data, accessed only through Laravel. See [FIREBASE_SETUP.md](FIREBASE_SETUP.md), [WORDPRESS_SETUP.md](WORDPRESS_SETUP.md), and [FINAL_QA_REPORT.md](FINAL_QA_REPORT.md).
