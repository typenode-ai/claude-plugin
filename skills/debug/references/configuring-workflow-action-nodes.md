# Action

An action makes exactly one `fetch` and returns the parsed provider body unchanged. Include semantic `mode:"read"|"write"` on add, and update it when the operation's effect changes. Judge the effect from provider behavior rather than the HTTP verb.

The publish validator admits this ordered skeleton:

1. Leading `const` declarations for upstream inputs, secret references and request construction. Secret declarations may be interleaved with request constants.
2. `const response = await fetch(url, init)` (or the one-argument form).
3. `const body = await response.json()` or `.text()`.
4. `const accepted = ...` using provider-documented acceptance.
5. `if (!accepted) throw new Error(...)` immediately afterward, with secret-free error text.
6. Final `return body`.

Build URL/query and JSON or form payloads as expressions in leading constants. Optional fields may use conditional object spreads. Bind `response`, `body` and `accepted` by those exact names. No second HTTP call, response reshaping, extra response branch, or additional statements belong in this skeleton.

## Complete GET example

```javascript
const customerId = nodes.input.body?.customer_id;
const token = secrets.CRM;
const url = `https://api.example.com/customers/${encodeURIComponent(customerId)}`;
const init = { headers: { Authorization: `Bearer ${token}` } };
const response = await fetch(url, init);
const body = await response.json();
const accepted = response.ok;
if (!accepted) throw new Error(`Customer lookup failed: ${response.status}`);
return body;
```

## Complete JSON write example

```javascript
const customer = nodes.fetch_customer.response.body;
const token = secrets.CRM;
const payload = { customer_id: customer.id, active: true };
const init = {
  method: "POST",
  headers: { Authorization: `Bearer ${token}`, "Content-Type": "application/json" },
  body: JSON.stringify(payload),
};
const response = await fetch("https://api.example.com/subscriptions", init);
const body = await response.json();
const accepted = response.ok;
if (!accepted) throw new Error(`Subscription update failed: ${response.status}`);
return body;
```

For a documented JSON 404 meaning no matching record, `const accepted = response.ok || response.status === 404` can accept that business outcome. A following transformation reads the recorded status and normalizes it. Select `.text()` for an empty/text endpoint instead of assuming every response contains JSON. An action's returned body becomes the exchange's `response.body`; downstream readers retain status and headers alongside it.

When writing step code or reading branch/loop outputs, read [runtime-data](runtime-data.md).

When configuring connections, credentials, or consequential actions, read [handoffs](handoffs.md).
