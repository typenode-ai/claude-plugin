---
name: debug
description: Diagnose failed, unexpected or waiting Typenode runs using their historical graph and exact step or loop output; verify authorized repairs.
---

# Debug workflow runs

First compare the request with the advertised Typenode tool capabilities. For a clearly unsupported task, explain the limitation and stop before calling any Typenode tools. For supported work, continue below.

1. Identify the specific run and its environment. Ask for the missing identity when several runs fit. Gather its read-only historical graph and exact failed or waiting step evidence. Read [troubleshooting](references/troubleshooting-workflow-errors.md) before interpreting that evidence, diagnosing the cause or recovering the run.
2. Classify the cause before opening category references. Trace the first incorrect producer using [runtime data](references/runtime-data.md); use [input/output](references/configuring-workflow-input-output-nodes.md) for ingress or schema mismatches.
3. Load the diagnosed branch: provider contract [research](references/researching-workflow-apis.md) and [action](references/configuring-workflow-action-nodes.md); pure computation [transformation](references/configuring-workflow-transformation-nodes.md); routing [decision](references/configuring-workflow-decision-nodes.md); iteration [loop](references/configuring-workflow-loop-nodes.md); pending input [wait/resume](references/configuring-workflow-wait-for-input-nodes.md); publication or live state [triggers/publication](references/configuring-workflow-triggers.md).
4. Read [repair](references/repair.md) for retries or ambiguous effects, and [handoffs](references/handoffs.md) for integration fixes. If a repair needs changes to roles or resource access, continue Configure. Explain the root cause, evidence and required action. Never treat a failed write's receipt as proof that the provider saw nothing.
5. For an authorized draft change, read [graph authoring](references/building-workflow-graph.md), apply the smallest cause-level repair, validate and read back. A new deployment or provider-writing run is a separate action; verify its authorization and actual result.
6. Finish with the cause, repair evidence and remaining uncertainty. Historical failures remain historical; a changed draft alone does not prove that a new run succeeded.
