---
title: 'Standard Transaction: Direct Integration'
description: Recipe Description
hidden: false
recipe:
  color: '#018FF4'
  icon: 🦉
---
```javascript JavaScript
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
   //Handle transaction completed case here
   res.status(200).send("Transaction has been complete");
   return true;
 }
};

```

# Initialize the purchase endpoint

<!-- javascript@13-15 -->

Initialize the purchase endpoint, grab the totalAmount from the request body.

# Setup your socket connection

<!-- javascript@7,8,43-53,78,80-85 -->

Declare the DEVICE_IP and DEVICE_PORT for use and initialize the connection to the Moneris Go device. 

NOTE: Your requests need to be pre-fixed with two-bytes containing the message request length or the request will be rejected automatically. The responses you receive will also contain a pre-fix of two-bytes that contain the response message length.

Create data and error events for your TCP socket connection. The data event will trigger every time a new payload is received. The error event will trigger if a network-level error occurs on the socket.

# Build and send transaction playload

<!-- javascript@23-40 -->

Build the transaction request object that you will send to your Moneris Go device.  

# Process a response from socket

<!-- javascript@57,59,91-96 -->

The logic for processing a response from the socket will be in the data event that was declared earlier. You first need to append the received payload to the buffer. 

After you append the data to the buffer, checks are needed to ensure you have enough data in the buffer to process a response. You will initially check to see if there is enough data for the messageLength value, which indicates the size of the following response. If there is enough data, you will grab the value from the buffer.

# Handle the transaction message

<!-- javascript@69-75,102-124 -->

Check to see if the Moneris Go device is available. If the status value in the response is “Terminal busy”, the device is unavailable. You can close the connection.

Check to see if there is an error in the response. If “errorDetails” exists within the object then an error has occurred during validation. You can close the connection.

Check to see if completed equals “false”. If that is the case, the transaction is not fulfilled. You will receive additional messages from the terminal; do not close the connection.

Check to see if completed equals “true”. If that is the case, then the transaction has finished. You can close the connection.

Disconnect from the terminal based on the transaction status