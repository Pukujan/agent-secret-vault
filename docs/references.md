# Official references checked

- [Infisical source and self-hosted Compose](https://github.com/Infisical/infisical)
- [Infisical production Compose](https://github.com/Infisical/infisical/blob/main/docker-compose.prod.yml) — official Compose includes server, PostgreSQL, and Redis; pin the image version.
- [Infisical environment template](https://github.com/Infisical/infisical/blob/main/.env.example) — sample encryption/auth values are explicitly non-production.
- [Infisical machine identities](https://infisical.com/blog/introducing-machine-identities) — Universal Auth exchanges client credentials for an API access token; roles scope API access.
- [Infisical CLI secret injection](https://infisical.com/docs/cli/commands/run) — inject secrets into a launched process without first writing a `.env` file.
- [Cloudflare public applications](https://developers.cloudflare.com/cloudflare-one/access-controls/applications/http-apps/) — Access gates an internet-reachable application.
- [Cloudflare one-time PIN](https://developers.cloudflare.com/cloudflare-one/integrations/identity-providers/one-time-pin/) — allowlisted email, single-use code, 10-minute code lifetime.
- [Agent Vault proxy transport warning](https://github.com/Infisical/agent-vault/blob/main/docs/agents/protocol.mdx) — its proxy URL is plain HTTP and intended for a trusted/private network; that is why this public multi-computer design uses Infisical's HTTPS API rather than exposing Agent Vault's proxy port.
