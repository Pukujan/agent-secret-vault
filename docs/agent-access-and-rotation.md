# Agent access and credential rotation

## One-time agent enrollment

1. Create one Infisical machine identity per agent or agent computer.
2. Assign it to this project and grant the required role. For management agents, grant only the organization/project API permissions they use; use project administration only when the agent needs to manage project configuration.
3. Configure Universal Auth for the identity. Keep its client ID and client secret in that machine's local credential store or existing agent secret mechanism.
4. Set the Infisical URL, project ID, environment, and path as non-secret agent configuration. The agent can then authenticate remotely, renew its API token according to policy, and use the CLI/API.
5. Confirm that the audit view attributes activity to the expected machine identity.

Infisical machine identities exchange a client ID and client secret for API access tokens. The token TTL is configurable; the upstream documentation's example defaults to 7,200 seconds. Keep the client secret per agent so a single identity can be rotated or disabled without re-enrolling every agent.

## Rotation policy

### Agent management credentials

- Keep the persistent Universal Auth client secret separate for each agent.
- Rotate a specific agent's client secret when its computer is replaced, the agent is retired, or the credential is exposed. A scheduled rotation can be added once deployment and local credential update are automated.
- Do not rotate all agents' credentials at once unless deliberately doing a global recovery; that creates unnecessary downtime.

### Provider keys

- Rotate real provider API keys through the provider's own console/API when supported, then update the matching Infisical value.
- Do not assume that Infisical can rotate arbitrary provider credentials. Enable a provider-specific rotation integration only after verifying its support and its effect on existing consumers.
- For Vast.ai, use a dedicated scoped key for this broker. Replacing the placeholder in an agent's environment does not rotate or revoke the Vast key.

### Fake/placeholder keys

Placeholders such as `__VAST_API_KEY__` are routing labels, not authentication credentials. Rotation is useful only if a real downstream system accepts the placeholder as a key and the broker maps it to a real stored value. In that case, make the placeholder random per agent/session and revoke the mapping when the session ends. Otherwise, rotate the actual machine-identity client secret or provider key instead.

## Removal and emergency revocation

When an agent or computer should lose access, disable its machine identity and revoke its active client secret/token through the Infisical dashboard/API. If a provider key itself may have leaked, revoke/reset it at the provider, replace the stored value, and then confirm consumers use the replacement.
