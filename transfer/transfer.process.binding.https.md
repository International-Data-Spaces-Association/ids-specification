# Transfer Process HTTPS Binding

## 1 Introduction

This specification defines a RESTful API over HTTPS for the [Transfer Process Protocol](./transfer.process.protocol.md).

The OpenAPI definitions for this specification can be accessed [here](TBD).

## 2 Provider Path Bindings

### 2.1 Prerequisites

1. The `<base>` notation indicates the base URL for a connector endpoint. For example, if the base connector URL is `connector.example.com`, the
   URL `https://<base>/transfer-processes/request` will map to `https//connector.example.com/transfer-processes/request`.

2. All request and response messages must use the `application/json` media type.

### 2.2 TransferErrorMessage

In the event of a client request error, the connector must return an appropriate HTTP 4xxx client error code. If an error body is returned it must be
a [TransferErrorMessage](./message/transfer.error.message.json) with the following properties:

| Field             | Type          | Description                                                         |
|-------------------|---------------|---------------------------------------------------------------------|
| processId         | UUID          | The transfer process unique id.                                     |
| correlationId     | UUID          | The correlation id if sent by the provider; otherwise not included. |
| code              | string        | An optional implementation-specific error code.                     |
| reasons           | Array[object] | An optional array of implementation-specific error objects.         |

### 2.2.1 State transition errors

If a client or provider connector makes a request that results in an invalid transfer process state transition as defined by the Transfer Process Protocol, it must return
an HTTP code 400 (Bad Request) with an `TransferErrorMessage` in the response body.

### 2.3 Authorization

All requests should use the `Authorizartion` header to include authorization data as specified by an authorization protocol such as [OAuth2](https://www.rfc-editor.org/rfc/rfc6749)
. The `Authorization` HTTP header is optional if the connector does not require authorization. This specification does not mandate the use of a particular authorization standard.

### 2.4 The provider `transfer-processes` resource

#### 2.4.1 GET

```
GET https://connector.provider.com/transfer-processes/:id

Authorization: ...

```

If the transfer process is found and the client is authorized, the provider connector must return an HTTP 200 (OK) response and a body containing
the [TransferProcess](./message/transfer.process.json):

```
{
  "@context":  "https://w3id.org/idsa/v5/context.json",
  "@id": "urn:uuid:71f8dfab-9337-4e9d-a4c7-524e04443f16",
  "@type": "ids:TransferProcess",
  "ids:correlationId": "urn:uuid:4a3ad65e-d78a-4200-a666-fc47aec32f2f",
  "ids:state": "REQUESTED"
} 
```

Predefined states are: `REQUESTED`, `STARTED`, `SUSPENDED`, `REQUESTED`, `COMPLETED`, and `TERMINATED`.

If the transfer process does not exist or the client is not authorized, the provider connector must return an HTTP 404 (Not Found) response.

### 2.5 The provider `transfer-processes/request` resource

#### 2.5.1 POST

A transfer process is started and placed in the `REQUESTED` state when a consumer POSTs a [TransferRequestMessage](./transfer.process.protocol.md#1-transferrequestmessage)
to `transfer-processes/request`:

 ```
 POST https://connector.provider.com/transfer-processes/request
 
 Authorization: ...
 
{
  "@context":  "https://w3id.org/idsa/v5/context.json",
  "@id": "urn:uuid:4a3ad65e-d78a-4200-a666-fc47aec32f2f",
  "@type": "ids:TransferRequestMessage",
  "ids:agreementId": "urn:uuid:e8dc8655-44c2-46ef-b701-4cffdc2faa44",
  "dct:format": "ids:s3+push",
  "dataAddress": {},
  "ids:callbackAddress": "https://......"
}
 ```

The `callbackAddress` property specifies the base endpoint `URL` where the client receives messages associated with the transfer process. Support for the `HTTPS` scheme is
required. Implementations may optionally support other URL schemes.

Callback messages will be sent to paths under the base URL as described by this specification. Note that provider connectors should properly handle the cases where a trailing `/`
is included with or absent from the `callbackAddress` when resolving full URL.

The @id is the correlation id that will be used for callback messages.

The provider connector must return an HTTP 201 (Created) response with the location header set to the location of the transfer process and a body containing
the [TransferProcess](./message/transfer.process.json) message:

 ```
 Location: /transfer-processes/urn:uuid:71f8dfab-9337-4e9d-a4c7-524e04443f16
 
{
  "@context":  "https://w3id.org/idsa/v5/context.json",
  "@id": "urn:uuid:71f8dfab-9337-4e9d-a4c7-524e04443f16",
  "@type": "ids:TransferProcess",
  "ids:correlationId": "urn:uuid:4a3ad65e-d78a-4200-a666-fc47aec32f2f",
  "ids:state": "REQUESTED"
}

 ```

Note that if the location header is not an absolute URL, it must resolve to an address that is relative to the base address of the request.

### 2.6 The provider `transfer-processes/:id/start` resource

#### 2.6.1 POST

The consumer connector can POST a [TransferStartMessage](./message/transfer.start.message.json) to attempt to start a transfer process after it has been suspended. If the transfer
process state is successfully transitioned, the producer must return HTTP code 200 (OK). The response body is not specified and clients are not required to process it.

### 2.7 The provider `transfer-processes/:id/completion` resource

#### 2.7.1 POST

The consumer connector can POST a [TransferCompletionMessage](./message/transfer.completion.message.json) to complete a transfer process. If the transfer
process state is successfully transitioned, the provider must return HTTP code 200 (OK). The response body is not specified and clients are not required to process it.

### 2.8 The provider `transfer-processes/:id/termination` resource

#### 2.8.1 POST

The consumer connector can POST a [TransferTerminationMessage](./message/transfer.termination.message.json) to terminate a transfer process. If the transfer
process state is successfully transitioned, the provider must return HTTP code 200 (OK). The response body is not specified and clients are not required to process it.

### 2.9 The provider `transfer-processes/:id/suspension` resource

#### 2.9.1 POST

The consumer connector can POST a [TransferSuspensionMessage](./message/transfer.suspension.message.json) to suspend a transfer process. If the transfer
process state is successfully transitioned, the producer must return HTTP code 200 (OK). The response body is not specified and clients are not required to process it.

## 3 Consumer Callback Path Bindings

### 3.1 Prerequisites

All callback paths are relative to the `callbackAddress` base URL specified in the `TransferRequestMessage` that initiated a transfer process. For example, if
the `callbackAddress` is specified as `https://connector.consumer/callback` and a callback path binding is `transfer-processes/:id/start`, the resolved URL will
be `https://connector.consumer.com/callback/transfer-processes/:id/start`.

### 3.2 The consumer `transfer-processes/:id/start` resource

#### 3.2.1 POST

The provider connector can POST a [TransferStartMessage](./message/transfer.start.message.json) to indicate the start of a transfer process. If the transfer
process state is successfully transitioned, the consumer must return HTTP code 200 (OK). The response body is not specified and clients are not required to process it.

### 3.3 The consumer `transfer-processes/:id/completion` resource

#### 3.3.1 POST

The provider connector can POST a [TransferCompletionMessage](./message/transfer.completion.message.json) to complete a transfer process. If the transfer
process state is successfully transitioned, the consumer must return HTTP code 200 (OK). The response body is not specified and clients are not required to process it.

### 3.4 The consumer `transfer-processes/:id/termination` resource

#### 3.4.1 POST

The provider connector can POST a [TransferTerminationMessage](./message/transfer.termination.message.json) to terminate a transfer process. If the transfer
process state is successfully transitioned, the consumer must return HTTP code 200 (OK). The response body is not specified and clients are not required to process it.

### 3.5 The consumer `transfer-processes/:id/suspension` resource

#### 3.5.1 POST

The provider connector can POST a [TransferSuspensionMessage](./message/transfer.suspension.message.json) to suspend a transfer process. If the transfer
process state is successfully transitioned, the consumer must return HTTP code 200 (OK). The response body is not specified and clients are not required to process it. 
