# Transformation

Transformation code is synchronous and returns data. It can map/filter/sort/reduce collections, normalize strings and dates, compute totals, parse values, combine guaranteed upstream results and shape the final output. It performs no HTTP calls, secret access or routing.

Map inputs to constants, declare the JSON-serializable result as `const output`, then `return output`; both names are required by validation. Intermediate Map/Set values are useful for grouping; convert them to objects or arrays before returning. Use explicit ISO strings for dates.

Example, after an action that accepts a documented not-found response:

```javascript
const status = nodes.fetch_customer.response.status;
const customer = nodes.fetch_customer.response.body;
const output = {
  found: status !== 404,
  customer_id: status === 404 ? null : customer.id,
};
return output;
```

Example, reshaping an API collection:

```javascript
const orders = nodes.fetch_orders.response.body.orders;
const summaries = orders.map((order) => ({
  id: order.id,
  total: order.items.reduce((sum, item) => sum + item.price * item.quantity, 0),
}));
const output = { orders: summaries };
return output;
```

Normalize values here before a decision compares them. For post-branch computation, inspect the merge snapshot and handle absent branch entries. For the final response, match every path to `output_json_schema`.

When writing step code or reading branch/loop outputs, read [runtime-data](runtime-data.md).
