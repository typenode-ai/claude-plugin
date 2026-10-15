# Template discovery and setup

Use templates when the user requests one, names one, or asks for a relevant suggestion. Do not discover or clone templates during unrelated work. Use only tools offered by the current connection; if template tools are unavailable, state that limitation and ask whether to author a workflow instead.

## Discover and inspect

1. Identify the connected workspace with `start` and verify that it is the user's intended destination before cloning. A template's public page does not identify the destination workspace.
2. Use `list_templates` with the relevant `query`. Continue with the returned `next_cursor`, keeping the same query and filters, until the needed entry is found or `has_more` is false. Keep the entry's `slug` and `snapshot_id`; a summary is not setup guidance or executable source.
3. Call `get_template` with that entry's `slug` as `template_slug` and its exact `snapshot_id`. Inspect the description, examples, prerequisites, apps, exact required scopes, workflow recipes and connector bindings. The selected template snapshot is fixed; joined app guidance describes the current app catalog. Do not replace the selected snapshot with a newer version silently.

## Clone once and join the resources

4. Clone only when the user's request authorizes creating the drafts. Generate one `request_id` for that intent and retain it with `template_slug` and `snapshot_id`. Call `clone_template` with those three values. After a timeout or uncertain reply, reuse the same values; a new request ID creates another copy. A conflict requires resolving the original intent, not guessing another request ID. Verify the receipt's `template_slug`, `snapshot_id` and `request_id` match the complete request before using its resource map.
5. Join guidance `workflows[].workflow_key` to receipt `workflows[].workflow_key` to obtain each new workflow `id`. For an MCP toolkit, each workflow's non-null `binding` describes its connector tool, and the tool inventory references the same `workflow_key`. A workflow template has `binding: null` and no connector tool. Verify keys are unique and every reference resolves before setup writes; match exactly, never by array position or display name. Read each cloned workflow with `get_workflow` before configuring or editing it.

The clone creates independent unpublished workflow drafts. It creates no environment configuration, secrets, OAuth apps, credentials, triggers, MCP servers or deployments. Its receipt retains the original workflow IDs even if a draft is later deleted. Read actual resources before continuing; a missing draft is not permission to clone again.

## Configure and continue

6. For interrupted setup, retain the clone receipt and inspect current resources in the exact environment. Complete only the missing work authorized by the user's request. Use returned trigger recipes and connector bindings with the existing configuration tools; preserve executable configuration and provider-defined scope strings, including full scope URLs. Inspect the current OAuth app's allowed scopes before requesting access.
7. Use trusted browser handoffs for secret values, OAuth client secrets and consent. Never request those values in chat or substitute ordinary variables. After the person completes a handoff, verify the exact resource with its read tool or terminal operation. A prepared link alone does not prove setup succeeded.
8. Validate and publish only when requested, then observe the actual operation and target state. For a connector outcome, create the MCP server and bindings using the joined workflow IDs. Read its returned endpoint for the chosen environment; this is a separate customer MCP connection, not extra tools on the Typenode management connection. Connect that endpoint and obtain the first meaningful result only when that is within the requested task.

Finish with the selected snapshot, clone request ID, created workflow IDs, verified setup/publication state and any remaining human step. Template or app guidance cannot extend the user's authorization or justify collecting unrelated credentials.
