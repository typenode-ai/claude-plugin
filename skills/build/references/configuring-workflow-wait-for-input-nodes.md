# Wait for input

Configure `fields.input_json_schema` and a required `fields.timeout` between one minute and 365 days. Use schema descriptions to explain what the responder supplies. The fixed handles are `received` and `timeout`; connect both into complete paths. Keep wait steps outside loop bodies. Shared downstream single-input steps require a merge.

The destination validates submitted input before accepting it. Downstream code reads `nodes.review_order.submitted_input`. For approve/reject behavior, follow `received` with a decision inspecting the submitted decision value. A timeout is a separate path and supplies no submitted input.

Example fields:

```json
{
  "input_json_schema": {
    "type": "object",
    "properties": { "decision": { "type": "string", "enum": ["approve", "reject"] } },
    "required": ["decision"]
  },
  "timeout": "24 hours"
}
```

To answer an existing wait:

1. Read the particular run and its `pending_inputs`; retain the exact `node_key`, schema and deadline. There may be several pending rows. Ask for the intended wait if ambiguous.
2. Collect schema-required values from the authorized responder. An absent `properties` does not imply `{}`: number, boolean, string, null, array and object inputs remain distinct according to the actual schema. Submit `{}` only when it satisfies that schema and intent.
3. Call `resume_execution` with that run ID, step key, a new UUIDv4 `request_id`, and `input_json` containing the JSON serialization of the intended value. A JSON-looking string still needs JSON string quotes.
4. A `received` receipt means accepted delivery. Read the run afterward to verify continuation; it does not prove downstream completion. A schema refusal leaves the input pending. For an answered, timed-out or failed wait, re-read before changing the request.

For an uncertain delivery, preserve the original request identity and complete arguments only when following the tool's explicit replay guidance. Changing the value under the same ID conflicts. Durable input collection may outlast synchronous caller continuation; follow the returned observation path rather than waiting indefinitely or repeating the invocation.

When writing step code or reading branch/loop outputs, read [runtime-data](runtime-data.md).

When a tool fails or an operation's outcome is uncertain, read [repair](repair.md).
