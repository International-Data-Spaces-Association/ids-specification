# 1 Terminology

This and the following section defines the core concepts, entities, and relationships that underpin a __dataspace__.

## Dataspace

A `Dataspace` is a set of technical services that facilitate interoperable `Dataset` sharing between entities.

## Dataspace Authority

A `Dataspace Authority` is an entity that manages a [Dataspace](#dataspace).

## Participant

A `Participant` is a [Dataspace](#dataspace) member that provides and/or consumes `Datasets`.

## ParticipantAgent

A `ParticipantAgent` is a technology system that performs operations on behalf of a [Participant](#participant).

## IdentityProvider

An `IdentityProvider` is a trusted technology system that creates, maintains, and manages identity information for a [Participant](#participant) and `ParticipantAgents`.

## CredentialIssuer

A `CredentialIssuer` is a trusted technology system that issues verifiable credentials for a [Participant](#participant) and `ParticipantAgents`.

## Observability, Traceability and Audit Logging

`Observability, Traceability and Audit Logging` of transactions, e.g. `Contract Negotiation`, `Data Transfer` and enforcement of access policies or usage policies, in a [Dataspace](#dataspace) can be a requirement.
If a  trusted technology system is required that records and verifies those domain events. This is not in the scope of the current version of the document and is subject of future work.

## DataspaceRegistrationService

A `DataspaceRegistrationService` is a technology system that maintains the state of [Participants](#participant) in a [Dataspace](#dataspace).

## Dataset

Data or a technical service that can be shared by a [Participant](#participant).

## Policy

A set of rules, duties, and obligations that define the terms of use for a `Dataset`.

## Offer

A concrete `Policy` associated with a specific `Dataset`.

## Agreement

A concrete `Policy` associated with a specific `Dataset` that has been signed by both the provider and consumer [Participants](#participant).

## Catalog

A collection of entries representing `Datasets` and their `Offers` that is advertised by a provider [Participant](#participant).

## CatalogService

A `ParticipantAgent` that makes a `Catalog` accessible to [Participants](#participant).

## Connector (DataService)

A `ParticipantAgent` that produces `Agreements` and manages `Dataset` sharing.

## Contract Negotiation

A set of interactions between a provider `Connector` and consumer `Connector` that establish an `Agreement`.

## Dataset Transfer

A set of interactions between a provider `Connector` and consumer `Connector` that give access to a `Dataset` under the terms of an `Agreement`.
