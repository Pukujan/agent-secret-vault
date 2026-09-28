# Deployment plan

## Current state

This repository does not contain a live service or a deployment secret. Hosting requires a persistent host and a public HTTPS hostname. A sleeping/free host may delay the first request; a powered-off home PC makes the service unavailable until it is started.

## Service foundation

Use Infisical's maintained self-host deployment, rather than copying its application into this project. Its upstream production Compose file includes the Infisical server, PostgreSQL, and Redis. Pin the Infisical image to a reviewed release before operating with real secrets; upstream marks `latest` as something to pin.

Official deployment entry points:

- [Infisical Docker Compose deployment](https://github.com/Infisical/infisical/blob/main/docker-compose.prod.yml)
- [Infisical environment template](https://github.com/Infisical/infisical/blob/main/.env.example)
- [Infisical deployment options](https://infisical.com/docs/self-hosting/deployment-options)

## Public access layout

1. Put the Infisical web/API service behind a TLS-terminating public HTTPS endpoint.
2. Enable Infisical account 2FA for the owner using email code or a mobile authenticator. Agents use their own machine identities for API requests; they do not need an interactive MFA prompt for every call.
3. If hosting from a home PC, Cloudflare Tunnel can publish the HTTPS hostname without opening inbound router ports. The tunnel provides reachability; Infisical still handles authentication.
4. Do not publish database or Redis ports.
5. Turn on the dashboard's audit logging and periodically inspect access events.

The delayed-agent workflow uses its persistent machine identity when the task starts, so no short-lived human code is requested while the job waits in a queue. Human MFA is for the owner's interactive dashboard session.

## Required runtime values

Generate unique production values for Infisical's `ENCRYPTION_KEY`, `AUTH_SECRET`, PostgreSQL password, public `SITE_URL`, and (if used) the Cloudflare tunnel token. Keep them in the deployment provider's secret settings or a local untracked `.env`. Never commit populated values.

Infisical's official `.env.example` contains sample keys and explicitly warns not to use them in production. This repo intentionally does not copy those sample values into a production `.env`.

## Backups and recovery

- Back up PostgreSQL on a schedule and test restoring it.
- Keep the encryption key and auth secret recoverable outside the GitHub repository; encrypted database backups are unusable if the encryption key is lost.
- Keep the owner account's MFA recovery method available.
- Document the public URL, service version, last backup, and the steps to revoke a machine identity.
