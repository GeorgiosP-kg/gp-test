---
name: InitializeThePurchaseEndpoint
---
Initialize the purchase endpoint, grab the totalAmount from the request body.

```
app.post("/purchase", (req, res) => {
 //Grab totalAmount from the request body
 const { totalAmount } = req.body;
}
```