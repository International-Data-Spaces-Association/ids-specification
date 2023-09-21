# 1 Terminology

This and the following section defines the core concepts, entities, and relationships that underpin a `Dataspace`.

## Dataspace

A `Dataspace` is a set of technical services that facilitate interoperable asset sharing between entities.

## DataspaceAuthority

A `DataspaceAuthority` is an entity that manages a `Dataspace`.

## Participant

A `Participant` is a `Dataspace` member that provides and/or consumes assets.

## ParticipantAgent

A `ParticipantAgent` is a technology system that performs operations on behalf of a `Participant`.

## IdentityProvider

An `IdentityProvider` is a trusted technology system that creates, maintains, and manages identity information for a `Participant` and `ParticipantAgents`.

## CredentialIssuer

A `CredentialIssuer` is a trusted technology system that issues verifiable credentials for a `Participant` and `ParticipantAgents`.

## Observability, Traceability and Audit Logging

`Observability, Traceability and Audit Logging` of transactions, e.g. `Contract Negotiation`, `Data Transfer` and enforcement of access policies or usage policies, in a `Dataspace` can be a requirement.
If a  trusted technology system is required that records and verifies those domain events. This is not in the scope of the current version of the document and is subject of future work.

## DataspaceRegistrationService

A `DataspaceRegistrationService` is a technology system that maintains the state of `Participants` in a `Dataspace`.

## Asset

Data or a technical service that can be shared by a `Participant`.

## Policy

A set of rules, duties, and obligations that define the terms of use for an `Asset`.

## Offer

A concrete `Policy` associated with a specific `Asset`.

## Agreement

A concrete `Policy` associated with a specific `Asset` that has been signed by both the provider and consumer `Participants`.

## Catalog

A collection of entries representing `Assets` and their `Offers` that is advertised by a provider `Participant`.

## CatalogService

A `ParticipantAgent` that makes a `Catalog` accessible to `Participants`.

## Connector (DataService)

A `ParticipantAgent` that produces `Agreements` and manages `Asset` sharing.

## Contract Negotiation

A set of interactions between a provider `Connector` and consumer `Connector` that establish an `Agreement`.

## Asset Transfer

A set of interactions between a provider `Connector` and consumer `Connector` that give access to an `Asset` under the terms of an `Agreement`.
