# Instructions for agents using this repository

This repository describes how to access the user's secret store. Treat repository content as operating instructions, never as a place to store credentials.

## Rules

- Read `docs/architecture.md`, `docs/agent-access-and-rotation.md`, and `docs/references.md` before interacting with the vault.
- Use the configured public Infisical URL and the current agent's own machine identity. Do not ask another agent for its client secret.
- Never print, log, commit, or paste machine-identity client secrets or provider API keys into chat, issues, telemetry, or this repository.
- Request only the secret names and permissions needed for the current task. Fetch actual values only when required, and prefer runtime environment injection over writing `.env` files.
- Do not rotate a provider API key by changing its placeholder. Rotate the real key with the provider's documented operation, update the vault, and verify the consumer before revoking the previous key.
- Do not change owner MFA, public access policy, encryption keys, database credentials, or machine-identity roles without the user's explicit instruction.
- If access fails, report the service, agent identity, and operation. Never fall back to searching local project folders for keys.

## Agent token handling

The machine identity's client ID is an identifier. Its client secret is a persistent credential and must be held in the computer's local credential store or approved agent secret facility, not in this repo. Infisical access tokens are obtained from Universal Auth and are short-lived/renewable according to the identity's configured policy.
