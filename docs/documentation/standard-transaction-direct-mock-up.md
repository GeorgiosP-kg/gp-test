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

Initialize the purchase endpoint, grab the **totalAmount** from the request body. Testing

```javascript
app.post("/purchase", (req, res) => {
 //Grab totalAmount from the request body
 const { totalAmount } = req.body;
}
```

<br />

***

<br />

## 2. Setup Your Socket Connection

Declare the **DEVICE\_IP** and **DEVICE\_PORT** for use and initialize the connection to the Moneris Go device.

**NOTE**: Your requests need to be prefixed with two-bytes containing the message request length, or the request will be rejected automatically. The responses you receive will also contain a prefix of two-bytes that contain the response message length.

```javascript
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

<br />

Create **data** and **error** events for your TCP socket connection. The **data** event will trigger every time a new payload is received. The **error** event will trigger if a network-level error occurs on the socket.

```javascript
client.on("data", () => {

});

client.on("error", (error) => {

});
```

<br />

***

<br />

## 3. Build and Send the Transaction Payload

Build the transaction request object that you will send to your Moneris Go device. The type of functionality that will be executed by your request is dependent on the "**action**" value that you send in the request. For a full list of all the possible fields for the request, visit the API spec at the **URL**.

```javascript
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

<br />

***

<br />

## 4. Process a Response from the Socket

The logic for processing a response from the socket will be in the **data** event that was declared earlier. You first need to append the received payload to the **buffer**.

```javascript
buffer = Buffer.concat([buffer, data]);
```

<br />

After you append the data to the **buffer**, checks are needed to ensure you have enough data in the **buffer** to process a response. You will initially check to see if there is enough data for the **messageLength** value, which indicates the size of the following response. If there is enough data, you will grab the value from the **buffer**.

```javascript
const getMessageLength = (buffer) => {
 if (buffer.length >= 2) {
   return buffer.readUInt16BE(0);
 }
 return undefined;
};

const messageLength = getMessageLength(buffer);
```

<br />

After you have the **messageLength** you will perform another check to see if you have enough data in the **buffer** for the complete JSON response. If that is the case, you will grab the data from the **buffer** and convert it to JSON.

```javascript
const readMessage = (buffer, messageLength) => {
 return JSON.parse(buffer.subarray(2, messageLength).toString());
};

const hasFullMessage = messageLength && messageLength <= buffer.length - 2;

if (!hasFullMessage) {
 //not enough data in the buffer, wait for next data event to try again
 return;
}

const transactionData = readMessage(buffer, messageLength);


Once a message has been processed, it needs to be removed from the buffer. 

buffer = buffer.subarray(messageLength + 2);
```

<br />

***

<br />

## 5. Handle the Transaction Message

Check to see if the Moneris Go device is available. If the **status** value in the response is “**Terminal busy**”, the device is unavailable. You can close the connection.

```javascript
if (transactionData.status === "Terminal busy") {
 //Handle Terminal busy error here
  
}
```

<br />

Check to see if there is an error in the response. If “**errorDetails**” exists within the object then an error has occurred during validation. You can close the connection.

```javascript
if (transactionData.data.response[0].errorDetails) {
 //Handle transaction validation error here
  
}
```

<br />

Check to see if **completed** equals “**false**”. If that is the case, the transaction is not fulfilled. You will receive additional messages from the terminal; do not close the connection.

```javascript
if (transactionData.data.response[0].completed === "false") {
 //Handle progress response here

}
```

<br />

Check to see if **completed** equals “**true**”. If that is the case, then the transaction has finished. Additionally check for **statusCode** of “**5207**” which indicates that the transaction was successful. You can close the connection.

```javascript
if (transactionData.data.response[0].completed === "true") {
 if (transactionData.data.response[0].statusCode === "5207") {
   //Handle transaction completed case here
  
 } else {
   //Handle transaction could not be complete case here
  
 }
 return true;
}
```

<br />

A number of additional **statusCodes** exist that need to be accounted for. For a full list of **statusCodes** visit **URL**.

Disconnect from the terminal based on the transaction status

```javascript
if (disconnectResponse) {
     //transaction has concluded, exit the processData function
     client.destroy();
}
```

<br />

***

<br />

## Full Code Example

Below is the full code example using Node and express listening on [http://localhost:3000](http://localhost:3000).

```javascript
import express from "express";
import { v4 as uuidv4 } from "uuid";
import net from "net";

const app = express();
const port = 3000;
const DEVICE_IP = "192.168.1.10"; //IP of your Device
const DEVICE_PORT = 1080; // Default port
const client = new net.Socket();

app.use(express.json());

app.post("/purchase", (req, res) => {
 //grab totalAmount for the transaction from the request body
 const { totalAmount } = req.body;

 //Initialize the buffer
 let buffer = Buffer.alloc(0);

 //Build the transaction object
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

 //Connect to the terminal and send the required prefix of two-bytes and message
 client.connect(DEVICE_PORT, DEVICE_IP, () => {
   const message = JSON.stringify(transactionPayload);
   const length = message.length;

   const header = Buffer.alloc(2);
   header[0] = length >> 8;
   header[1] = length & 0xff;

   client.write(header);
   client.write(message);
 });

 const processData = (data) => {
   //Append the data from response payload to the buffer
   buffer = Buffer.concat([buffer, data]);

   const messageLength = getMessageLength(buffer);
   const hasFullMessage = messageLength && messageLength <= buffer.length - 2;

   if (!hasFullMessage) {
     //not enough data in the buffer, wait for next data event to try again
     return;
   }

   const transactionData = readMessage(buffer, messageLength);

   const disconnectResponse = handleMessage(transactionData, res);
   buffer = buffer.subarray(messageLength + 2);

   if (disconnectResponse) {
     //transaction has concluded, exit the processData function
     client.destroy();
   }
 };

 client.on("data", processData);

 client.on("error", (error) => {
   console.error("Connection Error:", error);
   client.destroy();
   return;
 });
});

app.listen(port, () => {
 console.log(`ECR local backend listening on http://localhost:${port}`);
});

const getMessageLength = (buffer) => {
 if (buffer.length >= 2) {
   return buffer.readUInt16BE(0);
 }
 return undefined;
};

const readMessage = (buffer, messageLength) => {
 return JSON.parse(buffer.subarray(2, messageLength).toString());
};

const handleMessage = (transactionData, res) => {
 if (transactionData.status === "Terminal busy") {
   //Handle Terminal busy error here
   res.status(400).send("Pinpad is currently busy");
   return true;
 }

 if (transactionData.data.response[0].errorDetails) {
   //Handle transaction validation error here
   res.status(400).send("Request object validation failed");
   return true;
 }
 if (transactionData.data.response[0].completed === "false") {
   //Update buffer and get next message
   console.log("Terminal has sent back a progress response");
   return false;
 }

 if (transactionData.data.response[0].completed === "true") {
   if (transactionData.data.response[0].statusCode === "5207") {
     //Handle transaction completed case here
     res.status(200).send("Transaction has been complete");
   } else {
     //Handle transaction could not be complete case here
     res.status(400).send("Transaction could not be complete");
   }
   return true;
 }
};
```