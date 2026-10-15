# Architecture

Choose the smallest graph that expresses the required behavior:

| Step           | Responsibility                                             |
| -------------- | ---------------------------------------------------------- |
| Input          | Caller payload schema                                      |
| Action         | One provider HTTP exchange; semantic read/write mode       |
| Transformation | Pure data reshaping or computation                         |
| Decision       | Named routing handles; single-match or multi-match         |
| Merge          | Rejoin branches and expose their snapshot                  |
| Loop           | Collection/count/condition iteration with a supported body |
| Wait for input | Durable input collection with received/timeout paths       |
| Output         | Validate the final response contract                       |

Every complete path converges to the single reserved `output`, whose sole predecessor is a transformation. Use a merge when two or more branches feed the same single-input step. Merge performs no computation. Decide how the final response represents absent branches and expected provider negatives before authoring.

A loop body is constrained by graph validation; keep it linear with a single back-edge. Branch skip/break paths and reconverging merge bodies are unsupported. Place durable approval/input collection outside loops. Bound condition loops explicitly and plan provider pagination using actual response fields.

Document the step keys, handle destinations, upstream dependencies, input/output schemas and each external effect. Architecture is complete when every branch has a result and all data references are available on the path that uses them. Graph validation, rather than visual appearance, establishes supported topology.

When writing step code or reading branch/loop outputs, read [runtime-data](runtime-data.md).
