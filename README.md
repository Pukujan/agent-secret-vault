# Agent Secret Vault

Public source and agent guide for a remotely reachable, self-hosted secret store. The service is intended to be reachable over HTTPS from any of your machines; the source repository contains no credentials.

## Chosen foundation

Use [Infisical](https://github.com/Infisical/infisical) as the secret store, dashboard, API, machine-identity manager, and audit interface. This repository documents the deployment and the access contract for your agents instead of reimplementing those systems.

The management dashboard is reached through a public HTTPS URL and protected by Infisical's owner login with 2FA (email code or a mobile authenticator). Agent API access uses a persistent machine identity. Its client secret is the long-lived credential; the agent exchanges it for API access tokens according to the identity's configured TTL. Keep one identity per agent so one agent can be revoked or rotated without changing every machine. A Cloudflare Tunnel can provide the public hostname from a home PC without opening router ports; the service remains publicly reachable and Infisical handles authentication.

## Intended workflow

1. An agent reads this repository to find the vault URL, project, environment, and the documented CLI/API workflow. These identifiers are not secrets.
2. The agent authenticates with its own stored machine-identity credential and reads the telemetry and secret metadata its role allows.
3. When the task needs provider credentials, the agent fetches only the requested values and injects them into that task's process. It writes a local `.env` only when the user or tool specifically requires a file.
4. You manage the store and review audit events in the public dashboard from any device. Browser access uses your 2FA-protected login.
5. Agent machine-identity client secrets are rotated independently. Provider API keys are rotated at their provider when needed or when a supported provider-specific rotation workflow is configured.

## Security and rotation choices

- Do not commit runtime `.env`, database files, backups, machine-identity client secrets, provider keys, or Cloudflare tunnel credentials here.
- A permanent agent credential is an administrator capability. Give it the project permissions needed to manage the vault and telemetry; revoke or rotate it if an agent or computer should lose access.
- Each agent gets a distinct machine identity. Do not share one admin credential across all agents.
- Infisical Universal Auth uses a persistent client ID/client secret to obtain API access tokens. Those API access tokens have a configured lifetime; the persistent client secret is what makes agent access low-friction. Record and rotate that client secret, rather than pretending a temporary bearer token is permanent.
- Placeholder values such as `__VAST_API_KEY__` are not provider credentials. Rotating a placeholder alone does not revoke a real key. Rotate the actual stored provider key at the provider, then update the vault value.
- No generic rotator can safely replace every provider API key. Each provider must support key creation/revocation or have a verified rotation integration. Until configured, rotation is a tracked manual operation.

## Start here

- [Architecture and access model](docs/architecture.md)
- [Deployment and public access](docs/deployment.md)
- [Agent onboarding and token rotation](docs/agent-access-and-rotation.md)
- [Official references](docs/references.md)

## Status

Repository scaffold is ready. A live vault is not deployed yet: it needs a host with persistent PostgreSQL storage and a public HTTPS hostname. Do not put deployment credentials in this public repository.
