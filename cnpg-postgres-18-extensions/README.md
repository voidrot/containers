# CNPG PostgreSQL 18 extension image

This directory builds a CloudNativePG-compatible PostgreSQL 18 image based on the upstream CNPG `18-standard-trixie` image and adds these extensions:

- pg_cron
- pg_search
- pg_partman
- pgvector
- pg_trgm
- hypopg

Notes:
- `pg_trgm` is already shipped with upstream PostgreSQL, so this image verifies its presence rather than rebuilding it.
- `pg_search` is built from ParadeDB source (`v0.23.2`) with `cargo-pgrx` (`0.18.0`).
- `pg_cron`, `pg_partman`, `pgvector`, and `hypopg` are installed from the PGDG APT repository already configured in the CNPG base image.

Published image
- `ghcr.io/voidrot/cnpg-postgres-18-extensions:latest`
- also tagged as `pg18`, `v0.23.2`, dated schedule tags, and git SHA tags by the workflow

GitHub Actions
- Workflow: `.github/workflows/cnpg-postgres-18-extensions.yml`
- Triggers:
  - push to `main` when this image/workflow changes
  - pull requests touching this image/workflow
  - manual dispatch
  - weekly refresh every Monday at 04:17 UTC

Required GitHub repo settings
- Actions must be enabled.
- Repository packages permission must allow workflow pushes to GHCR using `GITHUB_TOKEN`.
- No extra secret is required for GHCR push if org/repo policy allows `GITHUB_TOKEN` package write.

Example CNPG usage

```yaml
cluster:
  imageName: ghcr.io/voidrot/cnpg-postgres-18-extensions:pg18
  postgresql:
    shared_preload_libraries:
      - pg_cron
      - pg_partman_bgw
      - pg_search
    parameters:
      cron.database_name: firecrawl
      pg_partman_bgw.dbname: firecrawl
```

Recommended verification after publishing

```bash
docker pull ghcr.io/voidrot/cnpg-postgres-18-extensions:pg18
docker run --rm ghcr.io/voidrot/cnpg-postgres-18-extensions:pg18 bash -lc '
  ls /usr/lib/postgresql/18/lib/{pg_cron.so,hypopg.so,pg_partman_bgw.so,vector.so,pg_search.so} &&
  ls /usr/share/postgresql/18/extension/{pg_cron.control,hypopg.control,pg_partman.control,vector.control,pg_search.control,pg_trgm.control}
'
```
