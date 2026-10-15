# Decision

A decision compares upstream values and returns `{ handles }`. Declare one to ten custom handle names on the step, with the automatically created `fallback`. Reserved names cannot be custom handles. Each returned name must exist and every outgoing handle must be connected.

Map values with leading constants and initialize `const handles = []`. For mutually exclusive outcomes use `if/else if/else`; first match wins. For independent outcomes use separate `if` statements, then add fallback if the array is empty. Keep reshaping, arithmetic, parsing, string normalization and array transformations in an upstream transformation.

Single-match example:

```javascript
const found = nodes.format_customer.found;
const handles = [];
if (found) {
  handles.push("found");
} else {
  handles.push("fallback");
}
return { handles };
```

Multi-match example outside a loop:

```javascript
const vip = nodes.format_customer.vip;
const highValue = nodes.format_customer.high_value;
const handles = [];
if (vip) {
  handles.push("vip");
}
if (highValue) {
  handles.push("high_value");
}
if (handles.length === 0) {
  handles.push("fallback");
}
return { handles };
```

Inside a loop, a decision may return only one handle and the body must satisfy loop topology guards. Single-match support does not imply supported skip/break/reconverging branch topology. For an approval, a decision follows the wait's `received` path and compares `submitted_input`; the wait itself exposes received/timeout, not approve/reject.

When writing step code or reading branch/loop outputs, read [runtime-data](runtime-data.md).
