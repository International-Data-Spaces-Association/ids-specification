# Transfer Process HTTPS Binding

## 1 Introduction

This specification defines a RESTful API over HTTPS for the [Transfer Process Protocol](./transfer.process.protocol.md).

The OpenAPI definitions for this specification can be accessed [here](https://app.swaggerhub.com/apis/eclipse-edc-bot/control-api/).

## 2 Provider Path Bindings

### 2.1 Prerequisites

1. The `<base>` notation indicates the base URL for a [Connector](../model/terminology.md#connector--data-service-) endpoint. For example, if the scheme is `https` and the full hostname is `connector.example.com`, the
   URL `<base>/transfers/request` will map to `https://connector.example.com/transfers/request`.

2. All request and response messages must use the `application/json` media type. Derived media types, e.g., `application/ld+json` may be exposed in addition.

### 2.2 TransferError

In the event of a client request error, the [Connector](../model/terminology.md#connector--data-service-) must return an appropriate HTTP 4xx client error code. If an error body is returned it must be
a [TransferError](./message/transfer-error.json) with the following properties:

| Field       | Type          | Description                                                 |
|-------------|---------------|-------------------------------------------------------------|
| consumerPid | UUID          | The [Transfer Process](../model/terminology.md#transfer-process) unique id on consumer side.            |
| providerPid | UUID          | The [Transfer Process](../model/terminology.md#transfer-process) unique id on [Provider](../model/terminology.md#provider) side.            |
| code        | string        | An optional implementation-specific error code.             |
| reasons     | Array[object] | An optional array of implementation-specific error objects. |

### 2.2.1 State transition errors

If a client or [Provider](../model/terminology.md#provider) makes a request that results in an invalid [Transfer Process](../model/terminology.md#transfer-process) state transition as defined by the Transfer Process Protocol, it must return
an HTTP code 400 (Bad Request) with an `TransferError` in the response body.

### 2.3 Authorization

All requests should use the `Authorization` header to include an authorization token. The semantics of such tokens are not part of this specification. The `Authorization` HTTP
header is optional if the [Connector](../model/terminology.md#connector--data-service-) does not require authorization.

### 2.4 The `transfers` Endpoint (provider-side)

#### 2.4.1 GET

```
GET https://connector.provider.com/transfers/:providerPid

Authorization: ...

```

If the [Transfer Process](./transfer.process.protocol.md#ack---transferprocess) is found and the client is authorized, the [Provider](../model/terminology.md#provider) must return an HTTP 200 (OK) response and a body containing
the [TransferProcess](./message/transfer-process.json):

```
{
  "@context":  "https://w3id.org/dspace/v0.8/context.json",
  "@type": "dspace:TransferProcess",
  "dspace:providerPid": "urn:uuid:a343fcbf-99fc-4ce8-8e9b-148c97605aab",
  "dspace:consumerPid": "urn:uuid:32541fe6-c580-409e-85a8-8a9a32fbe833",
  "dspace:state": "dspace:REQUESTED"
} 
```

Predefined states are: `REQUESTED`, `STARTED`, `SUSPENDED`, `REQUESTED`, `COMPLETED`, and `TERMINATED`.

If the [Transfer Process](./transfer.process.protocol.md#ack---transferprocess) does not exist or the client is not authorized, the [Provider](../model/terminology.md#provider) must return an HTTP 404 (Not Found) response.

### 2.5 The `transfers/request` Endpoint (provider-side)

#### 2.5.1 POST

A [Transfer Process](../model/terminology.md#transfer-process) is started and placed in the `REQUESTED` state when a consumer POSTs a [TransferRequestMessage](./transfer.process.protocol.md#1-transferrequestmessage)
to `transfers/request`:

 ```
 POST https://connector.provider.com/transfers/request
 
 Authorization: ...
 
{
  "@context":  "https://w3id.org/dspace/v0.8/context.json",
  "@type": "dspace:TransferRequestMessage",
  "dspace:consumerPid": "urn:uuid:32541fe6-c580-409e-85a8-8a9a32fbe833",
  "dspace:agreementId": "urn:uuid:e8dc8655-44c2-46ef-b701-4cffdc2faa44",
  "dct:format": "dspace:S3_AWS_PUSH",
  "dspace:dataAddress": {
    "@type": "dspace:DataAddress",
    "dspace:endpointType": "https://w3id.org/idsa/v4.1/HTTP",
    "dspace:endpoint": "http://example.com",
    "dspace:endpointProperties": [
      {
        "@type": "dspace:EndpointProperty",
        "dspace:name": "Authorization",
        "dspace:value": "Bearer TOKEN-ABCDEFG"
      }
    ]
  },
  "dspace:callbackAddress": "https://......"
}
 ```

The `callbackAddress` property specifies the base endpoint `URL` where the client receives messages associated with the [Transfer Process](../model/terminology.md#transfer-process). Support for the `HTTPS` scheme is
required. Implementations may optionally support other URL schemes.

Callback messages will be sent to paths under the base URL as described by this specification. Note that[Providers](../model/terminology.md#provider)) should properly handle the cases where a trailing `/`
is included with or absent from the `callbackAddress` when resolving full URL.

The [Provider](../model/terminology.md#provider) must return an HTTP 201 (Created) response with a body containing
the [TransferProcess](./message/transfer-process.json) message:

 ``` 
{
  "@context":  "https://w3id.org/dspace/v0.8/context.json",
  "@type": "dspace:TransferProcess",
  "dspace:providerPid": "urn:uuid:a343fcbf-99fc-4ce8-8e9b-148c97605aab",
  "dspace:consumerPid": "urn:uuid:32541fe6-c580-409e-85a8-8a9a32fbe833",
  "dspace:state": "dspace:REQUESTED"
}

 ```

### 2.6 The `transfers/:providerPid/start` Endpoint (provider-side)

#### 2.6.1 POST

The consumer [Connector](../model/terminology.md#connector--data-service-) can POST a [TransferStartMessage](./message/transfer-start-message.json) to attempt to start a [Transfer Process](../model/terminology.md#transfer-process) after it has been suspended. If the [Transfer
Process's](transfer.process.protocol.md#ack---transferprocess) state is successfully transitioned, the [Provider](../model/terminology.md#provider) must return HTTP code 200 (OK). The response body is not specified and clients are not required to process it.

### 2.7 The `transfers/:providerPid/completion` Endpoint (provider-side)

#### 2.7.1 POST

The consumer [Connector](../model/terminology.md#connector--data-service-) can POST a [TransferCompletionMessage](./message/transfer-completion-message.json) to complete a [Transfer Process](../model/terminology.md#transfer-process). If the [Transfer
Process's](transfer.process.protocol.md#ack---transferprocess) state is successfully transitioned, the [Provider](../model/terminology.md#provider) must return HTTP code 200 (OK). The response body is not specified and clients are not required to process it.

### 2.8 The `transfers/:providerPid/termination` Endpoint (provider-side)

#### 2.8.1 POST

The consumer [Connector](../model/terminology.md#connector--data-service-) can POST a [TransferTerminationMessage](./message/transfer-termination-message.json) to terminate a [Transfer Process](../model/terminology.md#transfer-process). If the [Transfer
Process's](transfer.process.protocol.md#ack---transferprocess) state is successfully transitioned, the [Provider](../model/terminology.md#provider) must return HTTP code 200 (OK). The response body is not specified and clients are not required to process it.

### 2.9 The `transfers/:providerPid/suspension` Endpoint  (provider-side)

#### 2.9.1 POST

The consumer [Connector](../model/terminology.md#connector--data-service-) can POST a [TransferSuspensionMessage](./message/transfer-suspension-message.json) to suspend a [Transfer Process](../model/terminology.md#transfer-process). If the [Transfer
Process's](transfer.process.protocol.md#ack---transferprocess) state is successfully transitioned, the [Provider](../model/terminology.md#provider) must return HTTP code 200 (OK). The response body is not specified and clients are not required to process it.

## 3 Consumer Callback Path Bindings

### 3.1 Prerequisites

All callback paths are relative to the `callbackAddress` base URL specified in the `TransferRequestMessage` that initiated a [Transfer Process](../model/terminology.md#transfer-process). For example, if
the `callbackAddress` is specified as `https://connector.consumer/callback` and a callback path binding is `transfers/:consumerPid/start`, the resolved URL will
be `https://connector.consumer.com/callback/transfers/:consumerPid/start`.

### 3.2 The `transfers/:consumerPid/start` Endpoint  (consumer-side)

#### 3.2.1 POST

The [Provider](../model/terminology.md#provider) can POST a [TransferStartMessage](./message/transfer-start-message.json) to indicate the start of a [Transfer Process](../model/terminology.md#transfer-process). If the [Transfer
Process's](transfer.process.protocol.md#ack---transferprocess) state is successfully transitioned, the consumer must return HTTP code 200 (OK). The response body is not specified and clients are not required to process it.

### 3.3 The `transfers/:consumerPid/completion` Endpoint (consumer-side)

#### 3.3.1 POST

The [Provider](../model/terminology.md#provider) can POST a [TransferCompletionMessage](./message/transfer-completion-message.json) to complete a [Transfer Process](../model/terminology.md#transfer-process). If the [Transfer
Process's](transfer.process.protocol.md#ack---transferprocess) state is successfully transitioned, the consumer must return HTTP code 200 (OK). The response body is not specified and clients are not required to process it.

### 3.4 The `transfers/:consumerPid/termination` Endpoint (consumer-side)

#### 3.4.1 POST

The [Provider](../model/terminology.md#provider) can POST a [TransferTerminationMessage](./message/transfer-termination-message.json) to terminate a [Transfer Process](../model/terminology.md#transfer-process). If the [Transfer
Process's](transfer.process.protocol.md#ack---transferprocess) state is successfully transitioned, the consumer must return HTTP code 200 (OK). The response body is not specified and clients are not required to process it.

### 3.5 The `transfers/:consumerPid/suspension` Endpoint (consumer-side)

#### 3.5.1 POST

The [Provider](../model/terminology.md#provider) can POST a [TransferSuspensionMessage](./message/transfer-suspension-message.json) to suspend a [Transfer Process](../model/terminology.md#transfer-process). If the [Transfer
Process's](transfer.process.protocol.md#ack---transferprocess) state is successfully transitioned, the consumer must return HTTP code 200 (OK). The response body is not specified and clients are not required to process it. 
