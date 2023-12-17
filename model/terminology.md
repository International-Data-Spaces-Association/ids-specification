# 1 Terminology

This and the following section defines the core concepts, entities, and relationships that underpin a __dataspace__.

## Agreement

A concrete [Policy](#policy) associated with a specific [Dataset](#dataset) that has been signed by both the provider and consumer [Participants](#participant).
An `Agreement` is a result of a [Contract Negotiation](../negotiation/contract.negotiation.protocol.md) and is associated with _exactly one_ [Dataset](#dataset).

## Catalog

A collection of entries representing [Datasets](#dataset) and their [Offers](#offer) that is advertised by a provider [Participant](#participant).

## Catalog Service

A [Participant Agent](#participant-agent) that makes a [Catalog](#catalog) accessible to [Participants](#participant).

## Connector (Data Service)

A [Participant Agent](#participant-agent) that produces [Agreements](#agreement) and manages [Dataset](#dataset) sharing.

## Consumer

A `Consumer` is a `ParticipantAgent` that requests access to an offered dataset.

## Contract Negotiation

A set of interactions between a provider [Connector](#connector--data-service-) and consumer [Connector](#connector--data-service-) that establish an [Agreement](#agreement).

## Dataset

Data or a technical service that can be shared by a [Participant](#participant).

## Dataspace

A `Dataspace` is a set of technical services that facilitate interoperable [Dataset](#dataset) sharing between entities.

## Dataspace Authority

A `Dataspace Authority` is an entity that manages a [Dataspace](#dataspace).

## Dataspace Registration Service

A `Dataspace Registration Service` is a technology system that maintains the state of [Participants](#participant) in a [Dataspace](#dataspace).

## Identity Provider

An `Identity Provider` is a trusted technology system that creates, maintains, and manages identity information for a [Participant](#participant) and [Participant Agents](#participant-agent).

## Offer

A concrete [Policy](#policy) associated with a specific [Dataset](#dataset).

## Participant

A `Participant` is a [Dataspace](#dataspace) member that provides and/or consumes [Datasets](#dataset).

## Participant Agent

A `Participant Agent` is a technology system that performs operations on behalf of a [Participant](#participant) that offers [Datasets](#dataset).

## Policy

A set of rules, duties, and obligations that define the terms of use for a [Dataset](#dataset). Also referred to as `Usage Policy`.

## Provider

A `Provider` is a `ParticipantAgent`

## Transfer Process

A set of interactions between a provider [Connector](#connector--data-service-) and consumer [Connector](#connector--data-service-) that give access to a [Dataset](#dataset) under the terms of an `[Agreement](#agreement).
