# Runtime data and code boundaries

| Source                         | Read path                                                                                                     |
| ------------------------------ | ------------------------------------------------------------------------------------------------------------- |
| Action exchange                | `nodes.fetch_customer.response.body`, `.response.status`, `.response.headers["content-type"]`, `.request.url` |
| Transformation result          | `nodes.format_customer.customer_id`                                                                           |
| Merge snapshot                 | `nodes.merge_results.fetch_customer.response.body`                                                            |
| Completed loop aggregate       | `nodes.process_customers.results`                                                                             |
| Wait response                  | `nodes.review_customer.submitted_input`                                                                       |
| Non-secret environment value   | `vars.REGION`                                                                                                 |
| Secret or connected credential | `secrets.CRM`, in action code only                                                                            |

Use exact returned step keys, with static dot access to `nodes.<key>`; bracket access on a value such as `response.headers["content-type"]` is valid. Keep `nodes`, `vars` and `secrets` as namespaces, rather than aliasing or destructuring them. Code can read upstream results that exist on its execution path. Sibling branches cannot read each other's output.

After branches converge, read conditional results through the merge snapshot. An absent branch entry is distinguishable from an executed result; direct access to a skipped step fails. A snapshot entry for an action still holds its exchange, so its provider data remains under `.response.body`.

Step code is JavaScript supplied through `code.javascript`. Imports, dynamic imports, `eval`, `console`, and timers/sleep are unavailable. Use `const`/`let` and JSON-serializable returned values. Keep provider HTTP work in actions, reshaping in transformations, routing in decisions, and iteration control in loops. Secrets never belong in returned values, URLs, error text, logs or literal code.

A failure stops the affected run and records evidence. Actions and transformations have a `success` handle; there is no error handle or `nodes.<step>__error` recovery output. Handle documented business outcomes through accepted provider responses followed by explicit transformation/decision logic.
