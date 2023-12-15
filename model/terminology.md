# 1 Terminology

This and the following section defines the core concepts, entities, and relationships that underpin a `Dataspace`.

## Agreement

A concrete `Policy` associated with a specific `Dataset` that has been signed by both the provider and consumer `Participants`.
An `Agreement` is a result of a [Contract Negotiation](../negotiation/contract.negotiation.protocol.md) and is associated with _exactly one_ `Asset`.

## Asset Transfer

A set of interactions between a provider `Connector` and consumer `Connector` that give access to an `Asset` under the terms of an `Agreement`.

## Catalog

A collection of entries representing `Datasets` and their `Offers` that is advertised by a provider `Participant`.

## CatalogService

A `ParticipantAgent` that makes a `Catalog` accessible to `Participants`.

## Connector (DataService)

A `ParticipantAgent` that produces `Agreements` and manages `Dataset` sharing.

## Consumer

A `Consumer` is a `ParticipantAgent` that requests access to an offered asset.

## Contract Negotiation

A set of interactions between a provider `Connector` and consumer `Connector` that establish an `Agreement`.

## Dataset

Data or a technical service that can be shared by a `Participant`.

## Dataset Transfer

A set of interactions between a provider `Connector` and consumer `Connector` that give access to a `Dataset` under the terms of an `Agreement`.

## Dataspace

A `Dataspace` is a set of technical services that facilitate interoperable `Dataset` sharing between entities.

## DataspaceRegistrationService

A `DataspaceRegistrationService` is a technology system that maintains the state of `Participants` in a `Dataspace`.

## DataspaceAuthority

A `DataspaceAuthority` is an entity that manages a `Dataspace`.

## IdentityProvider

An `IdentityProvider` is a trusted technology system that creates, maintains, and manages identity information for a `Participant` and `ParticipantAgents`.

## Message

A `Message`  is an instantiation of a `Message Type`.

## MessageType

A `MessageType` defines the structure and meaning of a `Message`.

## Offer

A concrete `Policy` associated with a specific `Dataset`.

## Participant

A `Participant` is a `Dataspace` member that provides and/or consumes `Datasets`.

## ParticipantAgent

A `ParticipantAgent` is a technology system that performs operations on behalf of a `Participant`. that offers an asset.

## Provider

A `Provider` is a `ParticipantAgent` offering `Datasets`.

## Policy

A set of rules, duties, and obligations that define the terms of use for a `Dataset`.
