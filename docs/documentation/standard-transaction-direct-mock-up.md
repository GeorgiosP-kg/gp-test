---
title: 'Standard Transaction: Direct Mock-up'
excerpt: >-
  This example illustrates how you would make a purchase using the Direct
  Integration method for the Moneris Go Solution.
deprecated: false
hidden: false
metadata:
  robots: index
---
## 1. Initialize the Purchase Endpoint

Initialize the purchase endpoint, grab the totalAmount from the request body.

```
app.post("/purchase", (req, res) => {
 //Grab totalAmount from the request body
 const { totalAmount } = req.body;
}
```

## 2. Setup Your Socket Connection

Declare the **DEVICE\_IP** and **DEVICE\_PORT** for use and initialize the connection to the Moneris Go device.

\*\*NOTE: \*\*Your requests need to be prefixed with two-bytes containing the message request length, or the request will be rejected automatically. The responses you receive will also contain a prefix of two-bytes that contain the response message length.

```
const DEVICE_IP = "192.168.1.10"; //IP of your Device
const DEVICE_PORT = 1080; // Default port

client.connect(DEVICE_PORT, DEVICE_IP, () => {
   const message = JSON.stringify(transactionPayload);
   const length = message.length;

   const header = Buffer.alloc(2);
   header[0] = length >> 8;
   header[1] = length & 0xff;

   client.write(header);
   client.write(message);
});
```

Create **data** and **error** events for your TCP socket connection. The **data** event will trigger every time a new payload is received. The **error** event will trigger if a network-level error occurs on the socket.

```
client.on("data", () => {

});

client.on("error", (error) => {

});
```

## 3. Build and Send the Transaction Payload

Build the transaction request object that you will send to your Moneris Go device. The type of functionality that will be executed by your request is dependent on the "**action**" value that you send in the request. For a full list of all the possible fields for the request, visit the API spec at the **URL**.

```
const orderId = uuidv4();
 const idempotencyKey = uuidv4();
 const transactionPayload = {
   apiVersion: "3.0",
   istConfigCode: "example_istConfigCode",
   dataId: "example_dataId",
   dataTimestamp: "1969-12-31 23:59:59",
   data: {
     request: [
       {
         orderId,
         idempotencyKey,
         //action determines the functionality of the request
         action: "purchase",
         totalAmount,
         progressStatus: "true",
       },
     ],
   },
 };
```

## 4. Process a Response from the Socket