# 1 Terminology

This and the following sections define the core concepts, entities, and relationships that underpin a __dataspace__ and its protocol.

## 1.1 Dataspace-specific Terms

### Agreement

A concrete [Policy](#policy) associated with a specific [Dataset](#dataset) that has been signed by both the [Provider](../model/terminology.md#provider) and consumer [Participants](#participant). An Agreement is a result of a [Contract Negotiation](../negotiation/contract.negotiation.protocol.md) and is associated with _exactly one_ [Dataset](#dataset).

### Catalog

A collection of entries representing [Datasets](#dataset) and their [Offers](#offer) that is advertised by a [Provider](../model/terminology.md#provider) [Participant](#participant).

### Catalog Service

A [Participant Agent](#participant-agent) that makes a [Catalog](#catalog) accessible to [Participants](#participant).

### Connector (Data Service)

A [Participant Agent](#participant-agent) that produces [Agreements](#agreement) and manages [Dataset](#dataset) sharing.

### Consumer

A [Participant Agent](#participant-agent) that requests access to an offered [Dataset](#dataset).

### Contract Negotiation

A set of interactions between a [Provider](#provider) and [Consumer](#consumer) that establish an [Agreement](#agreement). It is an instantiation of the state machine of a [Contract Negotiation Protocol](#contract-negotiation-protocol).

### Dataset

Data or a technical service that can be shared by a [Participant](#participant).

### Dataspace

A set of technical services that facilitate interoperable [Dataset](#dataset) sharing between entities.

### Dataspace Authority

An entity that manages a [Dataspace](#dataspace).

### Dataspace Registration Service

A technology system that maintains the state of [Participants](#participant) in a [Dataspace](#dataspace).

### Identity Provider

A trusted technology system that creates, maintains, and manages identity information for a [Participant](#participant) and [Participant Agents](#participant-agent).

### Offer

A concrete [Policy](#policy) associated with a specific [Dataset](#dataset).

### Participant

A [Dataspace](#dataspace) member that provides and/or consumes [Datasets](#dataset).

### Participant Agent

A technology system that performs operations on behalf of a [Participant](#participant) that offers a [Dataset](#dataset).

### Policy

A set of rules, duties, and obligations that define the terms of use for a [Dataset](#dataset). Also referred to as `Usage Policy`.

### Provider

A [Participant Agent](#participant-agent) that offers a [Dataset](../model/terminology.md#dataset).

### Transfer Process

A set of interactions between a [Provider](#provider) and [Consumer](#consumer) that give access to a [Dataset](#dataset) under the terms of an [Agreement](#agreement).

## 1.2 Protocol-specific Terms

### Contract Negotiation Protocol

A set of allowable [Message Type](#message-type) sequences defined as a state machine.

### Message

An instantiation of a [Message Type](#message-type).

### Message Type

A definition of the structure of a [Message](#message).

### Transfer Process Protocol

