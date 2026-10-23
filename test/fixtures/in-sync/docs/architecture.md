# Architecture

The build redraws this file with the bundle it has just built, so the check in CI must pass on it.

<details>
<summary>Diagram source</summary>

```diagram
flow "Checkout"
node browser client "Browser"
node api service "Orders API"
node db database "Orders DB"
browser -> api : "POST /orders"
api -> db : "insert"
```

</details>

<!-- diagram:start id="checkout" hash="3f33bba6106d" -->
<img alt="Checkout: 3 nodes, 2 connections." src="assets/diagrams/checkout.svg">

<details>
<summary>Text description</summary>

**Nodes:**

- Browser, client
- Orders API, service
- Orders DB, database

**Connections:**

- Browser to Orders API: POST /orders
- Orders API to Orders DB: insert

</details>
<!-- diagram:end -->
