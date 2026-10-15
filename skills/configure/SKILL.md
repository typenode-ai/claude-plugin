---
name: configure
description: Configure Typenode MCP servers and bindings, roles and resource access, environments, variables, secrets, OAuth apps and credentials, triggers or authorized workflow publication.
---

# Configure integrations and access

First compare the request with the advertised Typenode tool capabilities. For a clearly unsupported task, explain the limitation and stop before calling any Typenode tools. For supported work, continue below.

1. Identify the requested resource and outcome; read its current state before changing it. For runtime configuration, retain the exact environment ID and name. For template setup or continuation, read [templates](references/templates.md) and preserve the existing clone receipt. For caller or response changes, read [input/output](references/configuring-workflow-input-output-nodes.md).
2. For a custom MCP server or binding, read [MCP server setup](references/configuring-mcp-servers.md) before configuration. For roles, service accounts, access keys or resource sharing, read [access management](references/managing-workspace-access.md). These references define setup and access semantics; use the advertised tool schemas for arguments.
3. Read [handoffs](references/handoffs.md) before configuring integrations, secrets or consequential actions. Inspect existing variables, secret metadata, OAuth apps and credentials; change only the authorized missing or incorrect resource. For provider console setup, read [API research](references/researching-workflow-apis.md) and verify current official settings and exact OAuth scopes.
4. The person enters sensitive values or approves account access through the returned trusted interface. Wait for completion, then verify its designated resource or terminal operation. Preserve the intended name, environment, app and scopes. If verification is unavailable, state the uncertainty and ask the person rather than creating a replacement.
5. For triggers, publication or live-state changes, read [triggers/publication](references/configuring-workflow-triggers.md). Validate and observe the actual operation before reporting the requested result. For an existing waiting run, read [wait/resume](references/configuring-workflow-wait-for-input-nodes.md) and answer only the designated pending input.
6. Finish with verified resource identities, the target environment where relevant, and the observed setup, access or publication outcome. Name any remaining human step. For errors or ambiguous outcomes, read [repair](references/repair.md).

A custom MCP server is connected separately in the person's AI client. A workflow's provider credential serves a different purpose; it does not connect that server or grant access to its tools.
