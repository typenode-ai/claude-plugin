# Loop

A loop's synchronous code returns an array, a nonnegative integer count, or a boolean condition. Its fixed handles are `loop_body` and `done`. Every body uses the supported linear topology with exactly one back-edge to the loop. Merge bodies, distinct skip/break return paths and wait steps inside the body are unsupported. Validate the actual graph rather than inferring support from a sketch.

Use only `const` declarations in loop control code; `let` and `var` are rejected. Body steps follow their own category's rules.

| Return  | Behavior                                                     |
| ------- | ------------------------------------------------------------ |
| Array   | Iterate the values in order                                  |
| Count   | Iterate `0` through `N-1`                                    |
| Boolean | Evaluate before each body run; initial false means zero runs |

Seed a condition explicitly when at least one iteration is required. Bound pagination/condition work using verified provider termination fields and an explicit maximum.

```javascript
return nodes.fetch_users.response.body.users;
```

```javascript
return 5;
```

```javascript
return (
  loop === null ||
  (loop.result.length < 20 && loop.result[loop.result.length - 1].response.body.has_more === true)
);
```

The last example assumes the body's last step is an action with that provider response. Adapt the field and last-step shape to verified evidence. It permits at most 20 body runs; it is not a generic write-retry recipe.

| Context                         | Meaning                                        |
| ------------------------------- | ---------------------------------------------- |
| `loop.item`                     | Collection item; undefined for condition mode  |
| `loop.index` / `loop.iteration` | Zero-based index / one-based iteration         |
| `loop.first`                    | First body iteration                           |
| `loop.last` / `loop.length`     | Collection last/length; null in condition mode |
| `loop.result`                   | Previous body results                          |

In the loop control code, `loop` is null on first evaluation. In body code, the nearest loop context is available. `get_step_output.loop_iteration` is also one-based; preserve the actual selector returned by the output tool when reading subsequent pages, instead of guessing from a collapsed timeline.

After successful completion, `nodes.process_users.results` holds one result per body iteration. If the final body step is an action, each result remains an exchange, so its body is at `results[i].response.body`. The aggregate does not publish original items; retain them through their original upstream source. A failure propagates before a completed aggregate is assigned; recorded step evidence is not a successful partial loop result.

When writing step code or reading branch/loop outputs, read [runtime-data](runtime-data.md).
