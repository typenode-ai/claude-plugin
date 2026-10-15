# Input and output contracts

The reserved input is `input`; the reserved output is `output`. Include descriptions that explain the caller's fields and the returned result.

Input `fields.input_json_schema` defines the payload expected from the actual caller. Webhook, email and schedule admission are selected by the separate trigger's `trigger_type`. Tool invocation and transport envelopes have different roles. Schema property descriptions carry caller instructions. Collect only data needed for the task.

Output `fields.output_json_schema` defines the returned object. A literally empty schema `{}` is rejected. An object schema with empty `properties` is permitted; it does not require invented response fields. Set `required` and `additionalProperties` to the intended contract. The immediate predecessor must be a transformation that returns this shape; mismatches fail at runtime.

Example schema:

```json
{
  "type": "object",
  "properties": { "customer_id": { "type": "string" }, "found": { "type": "boolean" } },
  "required": ["found"],
  "additionalProperties": false
}
```

Test required fields, missing versus explicit null, nested types and every branch's final output. Define an explicit expected-negative response rather than making a provider failure look like a successful result.

When defining caller input or accessing trigger payloads, read [ingress](ingress.md).
