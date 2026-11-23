# SICKSAINT project status

Updated 2026-11-23.

## Package state

- Gothic, minimal editorial design with subtle CSS animations and `prefers-reduced-motion` support.
- `frontend/` contains the Next.js public site and Admin Panel.
- `backend/` contains a newly scaffolded Laravel 12 API. The original supplied working tree lacked a Laravel application; this scaffold is new and does not preserve an unavailable backend.
- `deployment/` documents independent Vercel and PHP-host targets.
- Firestore is the single application datastore; Laravel is the only application API/auth/authorization layer. Firebase Authentication is not used.
- WordPress remains the intended CMS for published editorial content.

## Still needed before production

Configure server-side Firestore IAM credentials and mail transport, configure and verify the WordPress CMS, set platform environment variables, run staging integration tests, and review Laravel 13 upgrade timing. No live deployment, DNS change, or production data write has been made. See [FINAL_QA_REPORT.md](FINAL_QA_REPORT.md).
