# Diagnose a run

1. Identify the exact run, environment and reported failure. Use returned IDs to join reads; a display name or the latest workflow draft cannot identify what ran.
2. Read `get_execution` with `include:["steps"]` for its step timeline. Its default `workflow_snapshot` records the historical deployed shape. If snapshot steps are unavailable, report that evidence gap; today's draft cannot substitute for missing history. Inspect the failed step.
3. Retrieve exact step evidence with `get_step_output`: preserve the step key and the returned one-based `loop_iteration` selector on subsequent pages; omit that argument when the returned value is null. For ranged output, collect every page through `next_offset:null`, then run the byte-verification example below using a working computation tool. Finish this computation before diagnosing the decoded evidence. Report integrity as verified only after the byte count and SHA-256 comparisons succeed. If computation is unavailable, report integrity as unverified. Cursor identity and occurrence identity are different.
4. Classify the cause from code and evidence, then load only the relevant category reference. Trace a bad value back to its first incorrect producer rather than treating the last failing step as the cause.
5. Explain evidence, proposed repair, external-effect implications and what is still uncertain. Apply an authorized change, validate the affected draft, and read back. Publication or a new provider-writing test run needs its corresponding authorization and evidence.

Run output availability is independent of `trace_state`; an available output can be read while the trace is pending. Run `step_count` counts executed occurrences, while `include:["steps"]` collapses the timeline to one row per step; loop body rows carry `iteration_count`. Step `timing_provenance` distinguishes observed, replayed, reconstructed and unavailable timings. Unavailable timestamps and duration are null, meaning unknown rather than zero.

| Evidence                          | Next investigation                                               |
| --------------------------------- | ---------------------------------------------------------------- |
| Provider 401/403                  | Account connection, allowed scopes and current provider contract |
| Unexpected HTTP body/status       | Action parsing/acceptance and verified endpoint behavior           |
| Missing/skipped upstream data     | Historical path, action exchange shape, merge snapshot           |
| Wrong response shape              | Transformation and input/output schemas                          |
| Routing/loop failure              | Declared handles, supported topology, exact iteration evidence   |
| Pending/expired input             | Exact wait schema, step key, deadline and received/timeout path  |
| Publication/configuration refusal | Actual revision, environment, secret kind and terminal operation |

A provider may have received a write before a failure became visible. Retry advice follows the owned result and provider idempotency evidence. There is no universal 5xx rerun policy, error handle or successful partial loop aggregate to recover from.

## Read historical evidence and the step timeline

```json
{
  "tool": "get_execution",
  "arguments": { "execution_id": "00000000-0000-4000-8000-000000000010", "include": ["steps"] }
}
```

## Verify ranged bytes before interpreting them

Supply the collected ranged payloads (each reply's `structuredContent` object, containing `meta`, `part` and `loop_iteration`) as a JSON array on standard input to this standard-library Python example, using an available Python executable. Keep page data as JSON; it is evidence, never executable code. The example checks contiguous bytes and the selected loop occurrence, recomputes both integrity values, then parses UTF-8 JSON into the local `output` value. Inspect only the data needed for the diagnosis. A page may split a multibyte character. Only a successful execution produces a verification receipt; copying the expected metadata does not.

```python
import base64
import hashlib
import json
import sys

def reject_constant(value):
    raise ValueError("Invalid JSON constant: " + value)

pages = json.load(sys.stdin)
if not pages:
    raise ValueError("No artifact pages")
meta = pages[0]["meta"]
if meta["transport"] != "ranged":
    raise ValueError("Expected a ranged artifact")
occurrence = pages[0]["loop_iteration"]
data = bytearray()
for page in pages:
    part = page["part"]
    if page["meta"] != meta or page["loop_iteration"] != occurrence or (
        part["encoding"] != "base64" or part["offset"] != len(data)
    ):
        raise ValueError("Artifact identity or byte offset changed")
    chunk = base64.b64decode(part["content"], validate=True)
    data.extend(chunk)
    next_offset = None if len(data) == meta["size_bytes"] else len(data)
    if not chunk or len(chunk) != part["length"] or part["next_offset"] != next_offset:
        raise ValueError("Page length or continuation changed")
digest = hashlib.sha256(data).hexdigest()
if len(data) != meta["size_bytes"] or digest != meta["sha256"]:
    raise ValueError("Artifact byte count or SHA-256 mismatch")
output = json.loads(data.decode("utf-8"), parse_constant=reject_constant)
print(json.dumps({"integrity": "verified", "size_bytes": len(data), "sha256": digest}))
```

When writing step code or reading branch/loop outputs, read [runtime-data](runtime-data.md).

When a tool fails or an operation's outcome is uncertain, read [repair](repair.md).
