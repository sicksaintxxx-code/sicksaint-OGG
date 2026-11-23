# WordPress editorial CMS setup

WordPress is the planned source for published articles, categories, tags, authors, and media. No WordPress instance or credentials were supplied, so live connectivity and publication are not verified.

## Public read API

Provision WordPress with HTTPS and public REST API access. Set its permalinks to Post name. In the Next.js environment, configure `WORDPRESS_SITE_URL` and optionally `WORDPRESS_API_URL` (typically `https://cms.example/wp-json/wp/v2`). The read adapter requests public published content and uses featured-image URLs from WordPress. Use properly licensed editorial images and maintain their photographer/agency credits in CMS content.

## Publishing from the admin workflow

The Laravel publisher needs a dedicated WordPress user with the minimum required publishing permission and an Application Password. Set `WORDPRESS_SITE_URL`, `WORDPRESS_API_URL`, `WORDPRESS_USERNAME`, and `WORDPRESS_APPLICATION_PASSWORD` only in the Laravel server environment. Keep credentials out of frontend variables, source control, logs, and screenshots.

An editor approval creates a pending WordPress post; the publish action publishes an approved post. Until WordPress is configured, publishing returns an error rather than claiming success. Verify this flow on a staging CMS before production.

## Revalidation

The frontend route `POST /api/revalidate` is protected by `WORDPRESS_WEBHOOK_SECRET`. Configure the same random secret in Vercel and the trusted CMS webhook. Send it in the `x-webhook-secret` request header. Do not expose it in a URL or `NEXT_PUBLIC_*` variable.
