# Manage workspace access

Read `list_capabilities` and the relevant current role or resource access before proposing a change. Reuse verified principal, role, resource and environment IDs; a display name or email is not an ID. Ask for a missing identity rather than inventing a member directory.

## Scope meanings

| `scope_kind` | Meaning | Coordinates |
| --- | --- | --- |
| `workspace` | Applicable resources across the workspace | No resource coordinates |
| `self` | Target Typenode resource owned by the acting principal | No resource coordinates |
| `resource` | One exact target resource | `resource_type` and `resource_id` |
| `parent` | Resources under one exact parent | Parent type and ID in `resource_type` and `resource_id` |
| `parent_self` | Resources whose parent has the named type and is owned by the acting principal | `resource_type`; no `resource_id` |

Use the capability catalog's eligibility and the advertised schema for allowed narrowing. Environment and expiry limits constrain access where supported. `self` and `parent_self` refer to Typenode ownership, not the owner or assignee of an external CRM record or help desk ticket. Provider-level row restrictions need separate enforcement; an agent-supplied owner ID is not verified identity.

## Change and verify

1. Confirm the intended capability, subject, resources and duration. Select the narrowest scope that satisfies the authorized task. Existing access keys and OAuth scopes can narrow effective access; they cannot add authority.
2. For roles, inspect the current definition. Updating `capabilities` replaces the entire set: retain entries the task intends to preserve. Assigning a role preserves other assigned roles. Follow any returned expansion confirmation before retrying.
3. For a direct grant, use resource sharing with the exact `workflow`, `skill` or `mcp_server` and subject. Creating a service account grants neither access nor a token. Personal access-key registration uses a trusted browser handoff; the agent does not receive the raw key. Use the tool's result and handoff guidance for completion.
4. Read back the changed role or exact resource-access relation. `check_capability` tests the current caller; it cannot impersonate the recipient. `list_principals_with_capability` inspects matching access sources, not a complete member directory. Verify actual caller discovery or invocation separately when relevant and authorized.

Sharing a server does not automatically grant invocation access to every bound workflow. The exposed tools do not include role unassignment or service-account key creation; explain those limits when requested. Use the exact consequence/confirmation protocol for revocation or deletion. Audit tools report who changed what; they are not a substitute for setup or effective-access verification.

When configuring connections, credentials, or consequential actions, read [handoffs](handoffs.md).

When a tool fails or an operation's outcome is uncertain, read [repair](repair.md).
