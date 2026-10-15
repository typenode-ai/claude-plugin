# Author the graph

1. Read `get_workflow` with `include:["steps"]` for an existing workflow; its default summary is not the graph. Preserve returned `steps[]` keys. Create a workflow only for an authorized new-work request.
2. Add or patch steps with `update_workflow_steps`. Supply `key`, `type`, a concise purpose `description`, and `code.javascript` or `fields` for that type. Action steps also require semantic `mode:"read"|"write"`. Only decisions declare custom handles; other types create fixed handles automatically.
3. Connect handles after their target steps exist. Respect the actual batch bound; add/configure and connect are phases that may span multiple atomic calls. A failed batch applies none of its operations.
4. Call `validate_workflow`, repair every reported diagnostic, then re-read with `include:["steps"]`. Report a validated draft as a draft; configuration and publication have separate completion evidence.

| Type                                 | Fixed handles                           |
| ------------------------------------ | --------------------------------------- |
| Input, action, transformation, merge | `success`                               |
| Decision                             | `fallback` plus declared custom handles |
| Loop                                 | `loop_body`, `done`                     |
| Wait for input                       | `received`, `timeout`                   |
| Output                               | None                                    |

Operations are `add_node`, `patch_node`, `remove_node`, `connect_node_handle` and `disconnect_node_handle`. Their inputs still use the contract's `node`/`node_key` coordinates even though reads expose `steps[]`. Copy the advertised operation schema instead of inventing spellings. Reserved `input` and `output` each exist exactly once. A single-input step cannot receive two branches directly; connect them through a merge with at least two incoming paths.

Example connection operation, inside the tool's `operations` array:

```json
{
  "op": "connect_node_handle",
  "source_node_key": "fetch_customer",
  "source_handle": "success",
  "target_node_key": "format_customer"
}
```

Input/output and wait steps use `fields`; action/transformation/decision/loop use `code.javascript`; merge needs no code. Inspect saved keys before patching or connecting. Keep descriptions meaningful for the person viewing the workflow.

## Complete graph read

```json
{
  "tool": "get_workflow",
  "arguments": { "workflow_id": "00000000-0000-4000-8000-000000000001", "include": ["steps"] }
}
```

When writing step code or reading branch/loop outputs, read [runtime-data](runtime-data.md).

When a tool fails or an operation's outcome is uncertain, read [repair](repair.md).
