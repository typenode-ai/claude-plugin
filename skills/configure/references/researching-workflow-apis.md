# Provider API research

Read primary provider documentation using the research tools available in this host. Verify the exact endpoint, method, authentication scheme, scopes, request encoding, required fields, successful body shape, accepted non-2xx business outcomes, pagination, limits and idempotency support. Reading a search snippet is insufficient evidence.

For OAuth setup, inspect the live registered app guidance and current provider console documentation: redirect URI, client type, authorization/token URLs, scopes and provider-specific parameters. Treat the client ID as public registration metadata and the client secret as trusted-entry data. A universal cached recipe cannot establish a provider's current requirements.

Produce a compact verified request/response contract with source URLs and unresolved questions. Map provider fields to the workflow's actual input and upstream outputs. Use synthetic examples with no credentials or private account payloads. If official evidence does not establish a behavior, mark it uncertain and settle it before creating a consequential action.

Use one action per HTTP exchange. Move response normalization into a transformation. Preserve the distinction between an expected negative business answer and an API failure. Do not invent a retry endpoint, assume POST means write, or infer idempotency from the presence of a header name.

When configuring connections, credentials, or consequential actions, read [handoffs](handoffs.md).
