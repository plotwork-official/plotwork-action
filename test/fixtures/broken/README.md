# Broken

Line 8 names a node that was never declared, so validate and check must report error E131 there.

```diagram
flow "Broken"
node web client "Web"
web -> queue
```
