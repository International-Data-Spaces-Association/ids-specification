# Contract Negotiation Protocol

## Introduction: Terms

This document outlines the key elements of the contract negotiation protocol. The following terms are used:

- A _**message type**_ defines the structure of a _message_.
- A _**message**_  is an instantiation of a _message type_.
- The _**contract negotiation protocol**_ is the set of allowable message type sequences and is defined as a state machine (CNP-SM).
- A _**contract negotiation (CN)**_ is an instantiation of the CNP-SM.
- A _**provider**_ is a participant agent that offers an asset.
- A _**consumer**_ is a participant agent that requests access to an offered asset.

## Contract Negotiation Protocol

A contract negotiation (CN) involves two parties, a _provider_ that offers one or more assets under a usage contract and _consumer_ that requests assets.
A CN is uniquely identified through an [IRI](https://www.w3.org/International/articles/idn-and-iri/). Each CN requires a newly generated IRI, which may not be used in a CN after a terminal state has been reached.
A CN progresses through a series of states, which are tracked by the provider and consumer using messages. A CN transitions to a state in response to an acknowledged message from
the counter-party. Both parties have the same state of the CN. In case the states differ, the CN is terminated and a new CN has to be initiated.

The CN states are:

- **REQUESTED** - A contract for an asset has been requested by the consumer and the provider has sent an ACK response.
- **OFFERED** - The provider has sent a contract offer to the consumer and the consumer has sent an ACK response.
- **ACCEPTED** - The consumer has accepted the latest contract offer and the provider has sent an ACK response.
- **AGREED** - The provider has sent an agreement to the consumer, and the consumer has sent an ACK response.
- **VERIFIED** - The consumer has sent an agreement verification to the provider and the provider has sent an ACK response.
- **FINALIZED** - The provider has sent a finalization message including his own agreement verification to the consumer and the consumer has sent an ACK response. Data is
  now available to the consumer.
- **TERMINATED** - The provider or consumer has placed the contract negotiation in a terminated state. A termination message has been sent by either of the participants and the
  other has sent an ACK response. This is a terminal state.

### Contract Negotiation State Machine

The CN state machine is represented in the following diagram. Note that transitions to the `TERMINATED` state may occur from any other state and are not shown for simplicity:

![](./contract.negotiation.state.machine.png)

Transitions marked with `C` indicate a message sent by the consumer, transitions marked with `P` indicate a provider message. Terminal states are final; the state machine may
not transition to another state. A new CN may be initiated if, for instance, the CN entered the `TERMINATED` state due to a network issue.

Following table gives non-normative examples of transitions:

| Origin State               | Actor  | Message                | Target State | Non-normative Example                                                                                                                                                                                                                                                                                                                    |
|----------------------------|--------|-------------------------------------------|--------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| INIT                       | C      | ContractRequestMessage                    | REQUESTED    | The consumer requests a contract, without previous direct interaction with provider, e.g. through the usage of a central cataloging system, which provides all necessary information about the provider, to directly request a data contract.                                                                                            |
| INIT                       | P      | ContractOfferMessage                      | OFFERED      | The provider sends a contract offer directly to a consumer without previous interaction, e.g. because the provider knows about desired data (e.g., consumer placed a search for data request in a catalog) or consumer/provider are in an existing business relationship and provider wants to ship information to its business partner. |
| REQUESTED                  | P      | ContractOfferMessage                      | OFFERED      | The provider sends an offering for a requested resource.                                                                                                                                                                                                                                                                                 |
| REQUESTED                  | P      | ContractAgreementMessage                  | AGREED       | The provider sends a contract agreement after a successful validation of the consumer's contract request.                                                                                           |
| OFFERED                    | C      | ContractRequestMessage                    | REQUESTED    | The consumer declines the offered contract and might sent a counter offer to the provider.                                                                                                                                                                                                                                               |
| OFFERED                    | C      | ContractNegotiationEventMessage:accepted  | ACCEPTED     | The consumer accepts to comply with the defined details in the contract.                                                                                                                                                                                                                                                                 |
| ACCEPTED                   | P      | ContractAgreementMessage                  | AGREED       | The provider sends a provider-signed contract agreement to the consumer.                                                                                                                                                                                                                                                                 |
| AGREED                     | C      | ContractAgreementVerificationMessage      | VERIFIED     | The consumer sends a both-sides signed contract agreement to the provider.                                                                                                                                                                                                                                                               |
| VERIFIED                   | P      | ContractNegotiationEventMessage:finalized | FINALIZED    | The provider assures the consumer to have received the signed agreement.                                                                                                                                                                                                                                                                 |
| \*/{TERMINATED, FINALIZED} | {C, P} | ContractNegotiationTerminationMessage     | FINALIZED    | The provider or consumer is not willing to continue any further negotiation process. This might happen e.g. in the case the provider doesnt want to place any offer (i.e. data) to the consumer.                                                                                                                                         |

## Message Types

The CN state machine is transitioned upon receipt and acknowledgement of a message. This section details those messages as abstract message types.

### Notes

- Concrete wire formats are defined by the protocol binding, e.g. HTTPS.
- All policy types (Offer, Agreement) must contain an unique identifier in the form of a URI. GUIDs can also be used in the form of URNs, for instance following the
  pattern <urn:uuid:{GUID}>.
- An ODRL Agreement must have a target property containing the asset id.

### 1. ContractRequestMessage

![](./message/diagram/contract-request-message.png)

**Sent by**: Consumer

**Resulting State**: REQUESTED, TERMINATED

**Example**: [Initiating ContractRequestMessage](./message/contract-request-message_initial.json), [ContractRequestMessage](./message/contract-request-message.json)

**Response**: [ACK or ERROR.](#response-types)

**Schema**: [ContractRequestMessageShape](./message/shape/contract-request-message-shape.ttl), [ContractRequestMessage JSON Schema](./message/schema/contract-request-message-schema.json), [ContractNegotiationShape](./message/shape/contract-negotiation-shape.ttl) and [ContractNegotiation JSON Schema](./message/schema/contract-negotiation-schema.json)

#### Description

The `ContractRequestMessage` is sent by a consumer to initiate a contract negotiation or to respond to a `ContractOfferMessage` sent by a provider.

#### Notes

- The consumer must include an `offer` property, which itself must have a `@id` property. If the message includes a `providerPid` property, the request will be associated with an existing contract
  negotiation and a consumer offer will be created using either the `offer` or `offer.@id` properties. If the message does not include a `providerPid`, a new contract negotiation
  will be created on provider side using either the `offer` or `offer.@id` properties and the provider selects an appropriate `providerPid`.

- An `offer.@id` will generally refer to an offer contained in a catalog. If the provider is not aware of the `offer.@id` value, it must respond with an error message.

- The dataset id is not technically required but included to avoid an error where the offer is associated with a different data set.

- `callbackAddress` is a URL indicating where messages to the consumer should be sent in asynchronous settings. If the address is not understood, the provider MUST return an
  UNRECOVERABLE error.


### 2. ContractOfferMessage

![](./message/diagram/contract-offer-message.png)

**Sent by**: Provider

**Resulting State**: OFFERED, TERMINATED

**Example**: [Initiating ContractOfferMessage](./message/contract-offer-message_initial.json), [ContractOfferMessage](./message/contract-offer-message.json)

**Response**: [ACK or ERROR.](#response-types)

**Schema**: [ContractOfferMessageShape](./message/shape/contract-offer-message-shape.ttl) and [ContractOfferMessage JSON Schema](./message/schema/contract-offer-message-schema.json)

#### Description

The `ContractOfferMessage` is sent by a provider to initiate a contract negotiation or to respond to a `ContractRequestMessage` sent by a consumer.

### Notes

If the message includes a `consumerPid` property, the request will be associated with an existing contract negotiation. If the message does not include a `consumerPid`, a new contract negotiation
will be created on consumer side and the consumer selects an appropriate `consumerPid`.

#### Notes

- The dataset id is not required but can be included when the provider initiates a contract negotiation.

### 3. ContractAgreementMessage

![](./message/diagram/contract-agreement-message.png)

**Sent by**: Provider

**Resulting State**: AGREED, TERMINATED

**Example**: [ContractAgreementMessage](./message/contract-agreement-message.json)

**Response**: [ACK or ERROR.](#response-types)

**Schema**: [ContractAgreementMessageShape](./message/shape/contract-agreement-message-shape.ttl) and [ContractAgreementMessage JSON Schema](./message/schema/contract-agreement-message-schema.json)

#### Description

The `ContractAgreementMessage` is sent by a provider when it agrees to a contract. It contains the complete contract agreement.

A `ContractAgreementMessage` must contain a `consumerPid` and a `providerPid`.

A `ContractAgreementMessage` must contain an ODRL `Agreement`.

An `Agreement` must contain a `dspace:timestamp` property defined as an XSD DateTime type.  

An `Agreement` must contain a `dspace:consumerId` and `dspace:providerId`. The contents of these
properties are a dataspace-specific unique identifier of the contract agreement parties. Note that these
identifiers are not necessarily the same as the identifiers of the participant agents negotiating the
contract (i.e. the "connectors").

### 4. ContractAgreementVerificationMessage


![](./message/diagram/contract-agreement-verification-message.png)

**Sent by**: Consumer

**Resulting State**: VERIFIED, TERMINATED

**Example**: [ContractAgreementVerificationMessage](./message/contract-agreement-verification-message.json)

**Response**: [ACK or ERROR.](#response-types)

**Schema**: [ContractAgreementVerificationMessageShape](./message/shape/contract-agreement-verification-message-shape.ttl) and the [ContractAgreementVerificationMessage JSON Schema](./message/schema/contract-agreement-verification-message-schema.json)

#### Description

The `ContractAgreementVerificationMessage` is sent by a consumer to verify the acceptance of a contract agreement. A provider responds with an error if the contract cannot be
validated or is incorrect.

A `ContractAgreementVerificationMessage` must contain a `consumerPid` and a `providerPid`.

### 5. ContractNegotiationEventMessage


![](./message/diagram/contract-negotiation-event-message.png)

**Sent by**: Provider or Consumer

**Resulting State**: FINALIZED, ACCEPTED, TERMINATED

**Example**: [ContractNegotiationEventMessage](./message/contract-negotiation-event-message.json)

**Response**: [ACK or ERROR.](#response-types)

**Schema**: [ContractNegotiationEventMessageShape](./message/shape/contract-negotiation-event-message-shape.ttl) and the [ContractNegotiationEventMessage JSON Schema](./message/schema/contract-negotiation-event-message-schema.json)

#### Description

When the `ContractNegotiationEventMessage` is sent by a provider with an `eventType` property set to `FINALIZED`, a contract agreement has been finalized and the associated asset
is accessible. The state machine is transitioned to the `FINALIZED` state. Other event types may be defined in the future. A consumer responds with an error if the contract
cannot be validated or is incorrect.

It is an error for a consumer to send a `ContractNegotiationEventMessage` with an eventType `FINALIZED` to the provider.

When the `ContractNegotiationEventMessage` is sent by a consumer with an `eventType` set to  `ACCEPTED`, the state machine is placed in the `ACCEPTED` state.

It is an error for a provider to send a `ContractNegotiationEventMessage` with an eventType `ACCEPTED` to the consumer.

Note that contract events are not intended for propagation of agreement state after a contract negotiation has entered a terminal state. It is considered an error for a consumer or
provider to send a contract negotiation event after the negotiation state machine has entered a terminal state.

A `ContractNegotiationEventMessage` must contain a `consumerPid` and a `providerPid`.

### 6. ContractNegotiationTerminationMessage

![](./message/diagram/contract-negotiation-termination-message.png)

**Sent by**: Consumer or Provider

**Resulting State**: TERMINATED

**Example**: [ContractNegotiationTerminationMessage](./message/contract-negotiation-termination-message.json)

**Response**: [ACK or ERROR.](#response-types)

**Schema**: [ContractNegotiationTerminationMessageShape](./message/shape/contract-negotiation-termination-message-shape.ttl) and the [ContractNegotiationTerminationMessage JSON Schema](./message/schema/contract-negotiation-termination-message-schema.json)

#### Description

The `ContractNegotiationTerminationMessage` is sent by a consumer or provider indicating it has cancelled the negotiation sequence. The message can be sent at any state of a negotiation
without providing an explanation. Nevertheless, the sender may provide a description to help the receiver.

A `ContractNegotiationTerminationMessage` must contain a `consumerPid` and a `providerPid`.

#### Notes

- A contract negotiation may be terminated for a variety of reasons, for example, an unrecoverable error was encountered or one of the parties no longer wishes to continue. A
  connector's operator may remove terminated contract negotiation resources after it has reached the terminated state.

- If an error is received in response to a `ContractNegotiationTerminationMessage`, the sending party may choose to ignore the error.

## Response Types

### Notes

- The `ACK` and `ERROR` response message types are mapped onto a protocol such as HTTPS. A description of an error might be provided in protocol-dependent forms, e.g. for an HTTPS
  binding in the request or response body.

### ACK - ContractNegotiation

![](./message/diagram/contract-negotiation.png)

**Sent by**: Consumer or Provider

**Example**: [ContractNegotiation](./message/contract-negotiation.json)

**Schema**: [ContractNegotiationShape](./message/shape/contract-negotiation-shape.ttl) and the [ContractNegotiationErrorMessage JSON Schema](./message/schema/contract-negotiation-schema.json)

#### Description

The `ContractNegotiation` is an object returned by a consumer or provider indicating a successful state change happened.

### ERROR - ContractNegotiationError

![](./message/diagram/contract-negotiation-error.png)

**Sent by**: Consumer or Provider

**Example**: [NegotiationError](./message/contract-negotiation-error.json)

**Schema**: [ContractNegotiationErrorShape](./message/shape/contract-negotiation-error-shape.ttl) and the [ContractNegotiationErrorMessage JSON Schema](./message/schema/contract-negotiation-error-schema.json)

#### Description

The `ContractNegotiationError` is an object returned by a consumer or provider indicating an error has occurred. It does not cause a state transition.

