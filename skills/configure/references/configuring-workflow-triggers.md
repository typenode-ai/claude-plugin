# Triggers and publication

A workflow has zero or one trigger definition. Read `get_workflow` with `include:["steps","triggers"]` and `list_triggers` before editing it. Match the input schema to the payload supplied by the trigger. Inspect the current advertised schemas for type-specific admission fields.

- **Webhook:** choose sync/async response behavior and required admission authentication. Secret-backed admission descriptors need an actual static secret in the target environment; a credential-backed secret is a different kind. Use trusted entry for its value.
- **Schedule:** configure its UTC cron expression and optional static input. Without static input, the producer supplies timing metadata. Convert a requested local-time schedule using verified timezone behavior; the offered contract itself specifies UTC cron.
- **Email:** configure the allowlisted sender policy. Read the deployed email address; draft intent is not an issued mailbox. Pausing blocks admission while retaining the published mailbox; re-enabling the same target needs no redeployment. A new publication generation may rotate the mailbox, while unpublish/delete retire it.

Create, update, delete and live-state operations are separate. `update_trigger` shallow-merges `config`; send changed fields only, but supply the complete replacement descriptor for `config.auth`. Deletion may return a destructive confirmation; use its exact consequence report. `set_trigger_state` changes live state, not the authored definition. Never infer a new live state from an edited draft.

## Publication procedure

1. Validate the workflow and verify required environment configuration.
2. Re-read the workflow/trigger and environment. Preserve the current `draft_revision`, trigger ID and definition revision, and the exact environment ID/name pair.
3. For authorized publication, call `create_deployment` using those preconditions, a fresh UUIDv4 `request_id`, explicit `trigger_state`, and explicit `publication_expires_at` (null or the intended bounded UTC timestamp). For an anonymous webhook, null requires unbounded `workflow.publish` and `workflow.invoke` authority; a bounded expiry stays within both authority ceilings. JWT expiry controls request freshness, not the publication ceiling. Use the advertised shape rather than guessing a target from a display name.
4. Observe the returned `operation_id` with `get_operation` until terminal. A 202/accepted receipt is not a successful deployment.
5. Report the terminal operation's environment, committed release and control outcome where supplied. This records that operation's result, not a fresh observation of current admission state. Authored trigger reads do not prove an environment is enabled/paused; retain that uncertainty when no authoritative live-state read is exposed. Only claim a live test if an authorized invocation actually ran and its result was checked.

A `STALE_REVISION` refusal remains bound to its `request_id`. Re-read the workflow and trigger; deploying changed revisions requires a new reviewed intent and request ID.

Synchronous calling has a bounded continuation window; durable waits can continue afterward. Use the tool's actual continuation/observation receipt rather than treating caller timeout as a failed run or promising implementation-specific pricing.

## Read the deployment-owned email address

Supply the exact environment; an authoring-only read does not reveal a deployment-owned address.

```json
{
  "tool": "list_triggers",
  "arguments": {
    "workflow_id": "00000000-0000-4000-8000-000000000001",
    "environment_name": "sandbox"
  }
}
```

When defining caller input or accessing trigger payloads, read [ingress](ingress.md).

When configuring connections, credentials, or consequential actions, read [handoffs](handoffs.md).

When a tool fails or an operation's outcome is uncertain, read [repair](repair.md).

Before choosing or changing webhook authentication, read [webhook-auth](webhook-auth.md).
