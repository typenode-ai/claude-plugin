# Repair and uncertain outcomes

1. Read the result variant. A validation report, refusal, confirmation requirement, browser handoff or accepted operation has different next steps. `is_valid:false` is a validation result with diagnostics, not a transport failure.
2. For a problem result, use its code, typed field coordinates and bounds, then follow `recovery.action` and the actually available `next_tools`. Correct the named input or graph defect before resubmitting. Read current state when the evidence is stale.
3. Distinguish safe read retry from protected write replay. A timeout, support `request_id`, or 5xx does not prove that a write failed or that rerunning a workflow is safe. Observe the returned operation/run or read back the exact resource. Replay only where the contract explicitly supports it, with the original identity and complete original arguments.
4. For a pending handoff, wait for the member and verify the designated outcome. For `ask_human`, `configure_connection` or `stop`, resolve that condition rather than inventing another tool or permission.
5. After an authorized repair, validate or read back the affected result. A new run is a separate consequential action when it may call providers; verify authorization and provider idempotency before proposing it. Report remaining uncertainty plainly.

List tools return canonical keys and cursors. Continue using the returned cursor with its original filters; do not invent cursors or join on display names. An accepted receipt is not a terminal result. A correlation ID helps support trace a failure and is not a replay identity unless that operation's contract explicitly says so.
