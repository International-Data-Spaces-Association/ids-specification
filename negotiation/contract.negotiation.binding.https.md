# Contract Negotiation HTTPS Binding

## 1 Introduction

This specification defines a RESTful API over HTTPS for the [Contract Negotiation Protocol](./contract.negotiation.protocol.md).

## 2 Provider Path Bindings

### 2.1 Prerequisites

1. The `<base>` notation indicates the base URL for a [Connector](../model/terminology.md#connector--data-service-) endpoint. For example, if the base [Connector](../model/terminology.md#connector--data-service-) URL is `connector.example.com`, the
   URL `https://<base>/negotiation/request` will map to `https//connector.example.com/negotiation/request`.

2. All request and response messages must use the `application/json` media type.

### 2.2 Contract Negotiation Error

In the event of a client request error, the [Connector](../model/terminology.md#connector--data-service-) must return an appropriate HTTP 4xx client error code. If an error body is returned it must be
a [ContractNegotiationError](./message/contract-negotiation-error.json) with the following properties:

| Field       | Type          | Description                                                                                          |
|-------------|---------------|------------------------------------------------------------------------------------------------------|
| consumerPid | UUID          | The [Contract Negotiation](../model/terminology.md#contract-negotiation) unique id on [Consumer](../model/terminology.md#consumer) side. |
| providerPid | UUID          | The [Contract Negotiation](../model/terminology.md#contract-negotiation) unique id on [Provider](../model/terminology.md#provider) side.                                                 |
| code        | string        | An optional implementation-specific error code.                                                      |
| reasons     | Array[object] | An optional array of implementation-specific error objects.                                          |

### 2.2.1 State transition errors

If a client or [Provider](../model/terminology.md#provider) makes a request that results in an invalid [Contract Negotiation's](./contract.negotiation.protocol.md#ack---contractnegotiation) state transition as defined by the Contract Negotiation Protocol, it must return
an HTTP code 400 (Bad Request) with an `ContractNegotiationError` in the response body.

### 2.3 Authorization

All requests should use the `Authorization` header to include an authorization token. The semantics of such tokens are not part of this specification. The `Authorization` HTTP
header is optional if the [Connector](../model/terminology.md#connector--data-service-) does not require authorization.

### 2.4 The provider `negotiations` resource

#### 2.4.1 GET

```
GET https://connector.provider.com/negotiations/:providerPid

Authorization: ...

```

If the [Contract Negotiation](../model/terminology.md#contract-negotiation) is found and the client is authorized, the [Provider](../model/terminology.md#provider) must return an HTTP 200 (OK) response and a body containing
the [ContractNegotiation](./message/contract-negotiation.json):

```
{
  "@context": "https://w3id.org/dspace/v0.8/context.json",
  "@type": "dspace:ContractNegotiation",
  "dspace:providerPid": "urn:uuid:dcbf434c-eacf-4582-9a02-f8dd50120fd3",
  "dspace:consumerPid": "urn:uuid:32541fe6-c580-409e-85a8-8a9a32fbe833",
  "dspace:state" :"REQUESTED"
}  
```

Predefined states are: `REQUESTED`, `OFFERED`, `ACCEPTED`, `AGREED`, `VERIFIED`, `FINALIZED`, and `TERMINATED`.

If the [Contract Negotiation](../model/terminology.md#contract-negotiation) does not exist or the client is not authorized, the [Provider](../model/terminology.md#provider) must return an HTTP 404 (Not Found) response.

### 2.5 The provider `negotiations/request` resource

#### 2.5.1 POST

A [Contract Negotiation](../model/terminology.md#contract-negotiation) is started and placed in the `REQUESTED` state when a [Consumer](../model/terminology.md#consumer) POSTs
a [ContractRequestMessage](./message/contract-request-message_initial.json) to `negotiations/request`:

```
POST https://connector.provider.com/negotiations/request

Authorization: ...

{
  "@context": "https://w3id.org/dspace/v0.8/context.json",
  "@type": "dspace:ContractRequest"
  "dspace:consumerPid": "urn:uuid:32541fe6-c580-409e-85a8-8a9a32fbe833",
  "dspace:dataset": "urn:uuid:3dd1add8-4d2d-569e-d634-8394a8836a88",
  "dspace:offerId": "urn:uuid:2828282:3dd1add8-4d2d-569e-d634-8394a8836a88",
  "dspace:callbackAddress": "https://..."
}
```

The `callbackAddress` property specifies the base endpoint `URL` where the client receives messages associated with the [Contract Negotiation](../model/terminology.md#contract-negotiation). Support for the `HTTPS` scheme is
required. Implementations may optionally support other URL schemes.

Callback messages will be sent to paths under the base URL as described by this specification. Note that [Providers](../model/terminology.md#provider) should properly handle the cases where a trailing `/`
is included with or absent from the `callbackAddress` when resolving full URL.

The [Provider](../model/terminology.md#provider) must return an HTTP 201 (Created) response with a body containing
the [ContractNegotiation](./message/contract-negotiation.json):

```
{
  "@context": "https://w3id.org/dspace/v0.8/context.json",
  "@type": "dspace:ContractNegotiation"
  "dspace:providerPid": "urn:uuid:dcbf434c-eacf-4582-9a02-f8dd50120fd3",
  "dspace:consumerPid": "urn:uuid:32541fe6-c580-409e-85a8-8a9a32fbe833",
  "dspace:state" :"REQUESTED"
}
```

### 2.6 The provider `negotiations/:providerPid/request` resource

#### 2.6.1 POST

A [Consumer](../model/terminology.md#consumer) may make an [Offer](../model/terminology.md#offer) by POSTing a [ContractRequestMessage](./message/contract-request-message.json) to `negotiations/:providerPid/request`:

```
POST https://connector.provider.com/negotiations/urn:uuid:dcbf434c-eacf-4582-9a02-f8dd50120fd3/offers

Authorization: ...

{
  "@context": "https://w3id.org/dspace/v0.8/context.jsonn",
  "@type": "dspace:ContractRequestMessage",
  "dspace:providerPid": "urn:uuid:dcbf434c-eacf-4582-9a02-f8dd50120fd3",
  "dspace:consumerPid": "urn:uuid:32541fe6-c580-409e-85a8-8a9a32fbe833",
  "dspace:offer": {
    "@type": "odrl:Offer",
    "@id": "...",
    "target": "urn:uuid:3dd1add8-4d2d-569e-d634-8394a8836a88"
  }
}
```

The [Consumer](../model/terminology.md#consumer) must include the `providerPid` and `consumerPid`. The [Consumer](../model/terminology.md#consumer) must include either the `offer` or `offerId` property.

If the message is successfully processed, the [Provider](../model/terminology.md#provider) must return and HTTP 200 (OK) response. The response body is not specified and clients are not required to process
it.

### 2.7 The provider `negotiations/:providerPid/events` resource

#### 2.7.1 POST

A [Consumer](../model/terminology.md#consumer) can POST a [ContractNegotiationEventMessage](./message/contract-negotiation-event-message.json) to `negotiations/:providerPid/events` to accept the current
[Provider's](../model/terminology.md#provider) [Offer](../model/terminology.md#offer). If the [Contract Negotiation's](./contract.negotiation.protocol.md#ack---contractnegotiation) state is successfully transitioned, the provider must return HTTP code 200 (OK). The response body is not specified and clients are not
required to process it.

If the current [Offer](../model/terminology.md#offer) was created by the [Consumer](../model/terminology.md#consumer), the [Provider](../model/terminology.md#provider) must return HTTP code 400 (Bad Request) with an `NegotiationErrorMessage` in the response body.

### 2.8 The provider `negotiations/:providerPid/agreement/verification` resource

#### 2.8.1 POST

The [Consumer](../model/terminology.md#consumer) can POST a [ContractAgreementVerificationMessage](./message/contract-agreement-verification-message.json) to verify an [Agreement](../model/terminology.md#agreement). If the [Contract Negotiation's](./contract.negotiation.protocol.md#ack---contractnegotiation) state is
successfully transitioned, the [Provider](../model/terminology.md#provider) must return HTTP code 200 (OK). The response body is not specified and clients are not required to process it.

```
POST https://connector.provider.com/negotiations/urn:uuid:a343fcbf-99fc-4ce8-8e9b-148c97605aab/agreement/verification

Authorization: ...

{
  "@context": "https://w3id.org/dspace/v0.8/context.json",
  "@type": "dspace:ContractAgreementVerificationMessage",
  "dspace:providerPid": "urn:uuid:dcbf434c-eacf-4582-9a02-f8dd50120fd3",
  "dspace:consumerPid": "urn:uuid:32541fe6-c580-409e-85a8-8a9a32fbe833",
}

```

### 2.9 The provider `negotiations/:providerPid/termination` resource

#### 2.9.1 POST

The [Consumer](../model/terminology.md#consumer) can POST a [ContractNegotiationTerminationMessage](./message/contract-negotiation-termination-message.json) to terminate a [Contract Negotiation](../model/terminology.md#contract-negotiation). If the [Contract Negotiation's](../model/terminology.md#contract-negotiation)
state is successfully transitioned, the [Provider](../model/terminology.md#provider) must return HTTP code 200 (OK). The response body is not specified and clients are not required to process it.

## 3 Consumer Callback Path Bindings

### 3.1 Prerequisites

All callback paths are relative to the `callbackAddress` base URL specified in the `ContractRequestMessage` that initiated a [Contract Negotiation](../model/terminology.md#contract-negotiation). For example, if
the `callbackAddress` is specified as `https://connector.consumer/callback` and a callback path binding is `negotiations/:consumerPid/offers`, the resolved URL will
be `https://connector.consumer.com/callback/negotiations/:consumerPid/offers`.

### 3.2 The consumer `negotiations/offers` resource

#### 3.2.1 POST

A [Contract Negotiation](../model/terminology.md#contract-negotiation) is started and placed in the `OFFERED` state when a [Provider](../model/terminology.md#provider) POSTs a
[ContractOfferMessage](./message/contract-offer-message_initial.json) to `negotiations/offers`:

```
POST https://connector.provider.com/negotiations/offers

Authorization: ...

{
  "@context": "https://w3id.org/dspace/v0.8/context.json",
  "@type": "dspace:ContractOfferMessage"
  "dspace:providerPid": "urn:uuid:dcbf434c-eacf-4582-9a02-f8dd50120fd3",
  "dspace:dataset": "urn:uuid:3dd1add8-4d2d-569e-d634-8394a8836a88",
  "dspace:offer": {
    "@type": "odrl:Offer",
    "@id": "...",
    "target": "urn:uuid:3dd1add8-4d2d-569e-d634-8394a8836a88"
  }
  "dspace:callbackAddress": "https://..."
}
```

The `callbackAddress` property specifies the base endpoint URL where the client receives messages associated with the [Contract Negotiation](../model/terminology.md#contract-negotiation). Support for the HTTPS scheme is required. Implementations may optionally support other URL schemes.

Callback messages will be sent to paths under the base URL as described by this specification. Note that [Consumers](../model/terminology.md#consumer) should properly handle the cases where a trailing / is included with or absent from the callbackAddress when resolving full URL.

The [Consumer](../model/terminology.md#consumer) must return an HTTP 201 (Created) response with a body containing the [Contract Negotiation](./contract.negotiation.protocol.md#ack---contractnegotiation):

```
{
  "@context": "https://w3id.org/dspace/v0.8/context.json",
  "@type": "dspace:ContractNegotiation"
  "dspace:providerPid": "urn:uuid:dcbf434c-eacf-4582-9a02-f8dd50120fd3",
  "dspace:consumerPid": "urn:uuid:32541fe6-c580-409e-85a8-8a9a32fbe833",
  "dspace:state" :"OFFERED"
}
```

### 3.3 The consumer `negotiations/:consumerPid/offers` resource

#### 3.3.1 POST

A [Provider](../model/terminology.md#provider) may make an [Offer](../model/terminology.md#offer) by POSTing a [ContractOfferMessage](./message/contract-offer-message.json) to the `negotiations/:consumerPid/offers` callback:

```
POST https://connector.consumer.com/callback/negotiations/urn:uuid:32541fe6-c580-409e-85a8-8a9a32fbe833/offers

Authorization: ...

{
  "@context": "https://w3id.org/dspace/v0.8/context.json",
  "@type": "dspace:ContractOfferMessage",
  "dspace:providerPid": "urn:uuid:dcbf434c-eacf-4582-9a02-f8dd50120fd3",
  "dspace:consumerPid": "urn:uuid:32541fe6-c580-409e-85a8-8a9a32fbe833",
  "dspace:offer": {
    "@type": "odrl:Offer",
    "@id": "...",
    "target": "urn:uuid:3dd1add8-4d2d-569e-d634-8394a8836a88"
  }
}
```

If the message is successfully processed, the [Consumer](../model/terminology.md#consumer) must return an HTTP 200 (OK) response. The response body is not specified and clients are not required to
process it.

### 3.4 The consumer `negotiations/:consumerPid/agreement` resource

#### 3.4.1 POST

The [Provider](../model/terminology.md#provider) can POST a [ContractAgreementMessage](./message/contract-agreement-message.json) to the `negotiations/:consumerPid/agreement` callback to create an [Agreement](../model/terminology.md#agreement). If the
[Contract Negotiation's](./contract.negotiation.protocol.md#ack---contractnegotiation) state is successfully transitioned, the [Consumer](../model/terminology.md#consumer) must return HTTP code 200 (OK). The response body is not specified and clients are not required to process it.

```
POST https://connector.consumer.com/negotiations/urn:uuid:32541fe6-c580-409e-85a8-8a9a32fbe833/agreement

Authorization: ...

{
  "@context": "https://w3id.org/dspace/v0.8/context.json",
  "@type": "dspace:ContractAgreementMessage",
  "dspace:providerPid": "urn:uuid:dcbf434c-eacf-4582-9a02-f8dd50120fd3",
  "dspace:consumerPid": "urn:uuid:32541fe6-c580-409e-85a8-8a9a32fbe833",
  "dspace:agreement": {
    "@type": "odrl:Agreement",
    "@id": "e8dc8655-44c2-46ef-b701-4cffdc2faa44",
    "dspace:consumerId": "...",
    "dspace:providerId": "...",
    }
  }
}
```

### 3.5 The consumer `negotiations/:consumerPid/events` resource

#### 3.5.1 POST

A [Provider](../model/terminology.md#provider) can POST a [ContractNegotiationEventMessage](./message/contract-negotiation-event-message.json) to the `negotiations/:consumerPid/events` callback with an `eventType`
of `FINALIZED` to finalize an [Agreement](../model/terminology.md#agreement). If the [Contract Negotiation's](./contract.negotiation.protocol.md#ack---contractnegotiation) state is successfully transitioned, the [Consumer](../model/terminology.md#consumer) must return HTTP code 200 (OK). The response body is not
specified and clients are not required to process it. 

### 3.6 The consumer `negotiations/:consumerPid/termination` resource

#### 3.6.1 POST

The [Provider](../model/terminology.md#provider) can POST a [ContractNegotiationTerminationMessage](./message/contract-negotiation-termination-message.json) to terminate a [Contract Negotiation](../model/terminology.md#contract-negotiation). If the [Contract Negotiation's](./contract.negotiation.protocol.md#ack---contractnegotiation)
state is successfully transitioned, the [Consumer](../model/terminology.md#consumer) must return HTTP code 200 (OK). The response body is not specified and clients are not required to process it.
