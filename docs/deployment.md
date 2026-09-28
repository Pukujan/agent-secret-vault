# Deployment plan

## Current state

This repository does not contain a live service or a deployment secret. Hosting requires a persistent host, a public HTTPS hostname, and owner-controlled Cloudflare configuration. A sleeping/free host may delay the first request; a powered-off home PC makes the service unavailable until it is started.

## Service foundation

Use Infisical's maintained self-host deployment, rather than copying its application into this project. Its upstream production Compose file includes the Infisical server, PostgreSQL, and Redis. Pin the Infisical image to a reviewed release before operating with real secrets; upstream marks `latest` as something to pin.

Official deployment entry points:

- [Infisical Docker Compose deployment](https://github.com/Infisical/infisical/blob/main/docker-compose.prod.yml)
- [Infisical environment template](https://github.com/Infisical/infisical/blob/main/.env.example)
- [Infisical deployment options](https://infisical.com/docs/self-hosting/deployment-options)

## Public access layout

1. Put the Infisical web service behind a TLS-terminating public HTTPS endpoint.
2. Require Cloudflare Access for the dashboard/browser login, limited to the owner's email. Use email one-time PIN for low friction, or an identity provider with MFA.
3. Permit agent API requests to the public HTTPS app endpoint using each agent's Infisical machine identity. The application API still validates the identity and its assigned project/organization permissions.
4. Do not publish database or Redis ports.
5. Turn on the dashboard's audit logging and periodically inspect access events.

Cloudflare's public app pattern provides a public hostname with Access login in front. Its email PIN is single-use and expires after 10 minutes. The delayed-agent workflow should request approval only once the task is running, not when it enters a queue.

## Required runtime values

Generate unique production values for Infisical's `ENCRYPTION_KEY`, `AUTH_SECRET`, PostgreSQL password, public `SITE_URL`, and the Cloudflare tunnel token. Keep them in the deployment provider's secret settings or a local untracked `.env`. Never commit populated values.

Infisical's official `.env.example` contains sample keys and explicitly warns not to use them in production. This repo intentionally does not copy those sample values into a production `.env`.

## Backups and recovery

- Back up PostgreSQL on a schedule and test restoring it.
- Keep the encryption key and auth secret recoverable outside the GitHub repository; encrypted database backups are unusable if the encryption key is lost.
- Keep the owner account's MFA recovery method available.
- Document the public URL, service version, last backup, and the steps to revoke a machine identity.
