# Catalog Protocol

## 1 Introduction: Terms

This document outlines the Catalog Protocol. The used terms are described [here](../model/terminology.md).

The Catalog Protocol defines how a [Catalog](../model/terminology.md#catalog) is requested from a [Catalog Service](../model/terminology.md#catalog-service) by a [Consumer](../model/terminology.md#consumer) using an abstract message exchange format. The concrete message exchange wire
format is defined in the binding specifications.

## 2 Message Types

All messages must be serialized in JSON-LD compact form as specified in the [JSON-LD 1.1 Processing Algorithms and API](https://www.w3.org/TR/json-ld11-api/#compaction-algorithms).
Further [Dataspace](../model/terminology.md#dataspace) specifications may define additional optional serialization formats.

### 2.1 CatalogRequestMessage

![](./message/diagram/catalog-request-message.png)

**Sent by**: [Consumer](../model/terminology.md#consumer)

**Example**: [CatalogRequestMessage](./message/catalog-request-message.json)

**Response**: [Catalog](#22-catalog) containing the [DCAT Catalog](https://www.w3.org/TR/vocab-dcat-3/#Class:Catalog).

**Schema**: [CatalogRequestMessageShape](./message/shape/catalog-request-message-shape.ttl) and the [CatalogRequestMessage JSON Schema](./message/schema/catalog-request-message-schema.json)

#### Description

The `CatalogRequestMessage` is message sent by a [Consumer](../model/terminology.md#consumer) to a [Catalog Service](../model/terminology.md#catalog-service). The [Catalog Service](../model/terminology.md#catalog-service) must respond with a [Catalog](../model/terminology.md#catalog), which is a
valid instance of a [DCAT Catalog](https://www.w3.org/TR/vocab-dcat-3/#Class:Catalog).

The `CatalogRequestMessage` may have a `filter` property which contains an implementation-specific query or filter expression type supported by the [Catalog Service](../model/terminology.md#catalog-service).

The [Catalog Service](../model/terminology.md#catalog-service) may require an authorization token. Details for including that token can be found in the relevant Catalog Binding Specification. Similarly, pagination may
be defined in the relevant Catalog Binding Specification.


### 2.2 Catalog

![](./message/diagram/catalog.png)

**Sent by**: [Provider](../model/terminology.md#provider)

**Example**: [Catalog](./message/catalog.json)


**Response**: OK or ERROR

**Schema**: [CatalogShape](./message/shape/dcat-shapes.ttl) and the [Catalog JSON Schema](./message/schema/catalog-schema.json)

#### Description

The [Catalog](../model/terminology.md#catalog) contains all [Datasets](#31-dataset) which the requester shall see.


### 2.3 CatalogError

![](./message/diagram/catalog-error.png)

**Sent by**: [Consumer](../model/terminology.md#consumer) or [Provider](../model/terminology.md#provider)

**Example**: [CatalogError](./message/catalog-error.json)

**Response**: OK or ERROR

**Schema**: [CatalogErrorShape](./message/shape/catalog-error-shape.ttl) and the [CatalogError JSON Schema](./message/schema/catalog-error-schema.json)

#### Description

A Catalog Error Message is used when an error occurred after a `CatalogRequestMessage` and the [Provider](../model/terminology.md#provider) can not provide its [Catalog](../model/terminology.md#catalog) to the requester.

### 2.4 DatasetRequestMessage

**Sent by**: [Consumer](../model/terminology.md#consumer)

**Example**: [DatasetRequestMessage](./message/dataset-request-message.json)

**Response**: [Dataset](#25-dataset) containing the [DCAT Dataset](https://www.w3.org/TR/vocab-dcat-3/#Class:Dataset).

**Schema**: [DatasetRequestMessageShape](./message/shape/dataset-request-message-shape.ttl) and the [DatasetRequestMessage JSON Schema](./message/schema/dataset-request-message-schema.json)

#### Description

The `DatasetRequestMessage` is message sent by a [Consumer](../model/terminology.md#consumer) to a [Catalog Service](../model/terminology.md#catalog-service). The [Catalog Service](../model/terminology.md#catalog-service) must respond with a `Dataset,` which is a
valid instance of a [DCAT Dataset](https://www.w3.org/TR/vocab-dcat-3/#Class:Dataset).

The `DatasetRequestMessage` must have a [Dataset](../model/terminology.md#dataset) property which contains the id of the [Dataset](../model/terminology.md#dataset).

The [Catalog Service](../model/terminology.md#catalog-service) may require an authorization token. Details for including that token can be found in the relevant Catalog Binding Specification.


### 2.5 Dataset

![](./message/diagram/dataset.png)

**Sent by**: [Provider](../model/terminology.md#provider)

**Example**: [Dataset](./message/dataset.json)


**Response**: OK or ERROR

**Schema**: [DatasetShape](./message/shape/dcat-shapes.ttl) and the [Dataset JSON Schema](./message/schema/dataset-schema.json)

## 3 DCAT Vocabulary Mapping

This section describes how the DSP Information Model maps to DCAT resources.

### 3.1 Dataset

A [Dataset](../model/terminology.md#dataset) is a [DCAT Dataset](https://www.w3.org/TR/vocab-dcat-3/#Class:Dataset) with the following attributes:

#### odrl:hasPolicy

A [Dataset](../model/terminology.md#dataset) must have 1..N `hasPolicy` attributes that contain an [ODRL `Offer`](https://www.w3.org/TR/odrl-vocab/#term-Offer) defining the [Usage Policy](../model/terminology.md#policy) associated with the [Dataset](../model/terminology.md#dataset). Offers must NOT contain any
target attributes. The target of an [Offer](../model/terminology.md#offer) is the associated [Dataset](../model/terminology.md#dataset).

> Note: As `odrl:hasPolicy rdfs:domain odrl:Asset`, each [Dataset](../model/terminology.md#dataset) is also an `odrl:Asset` from an ODRL perspective.

### 3.2 Distributions

A [Dataset](../model/terminology.md#dataset) may contain 0..N [DCAT Distributions](https://www.w3.org/TR/vocab-dcat-3/#Class:Distribution). Each distribution must have at least one `DataService` which specifies where
the distribution is obtained. Specifically, a `DataService` specifies the endpoint for initiating a [Contract Negotiation](../model/terminology.md#contract-negotiation) and [Transfer Process](../model/terminology.md#transfer-process).

A Distribution may have 0..N `hasPolicy` attributes that contain an [ODRL `Offer`](https://www.w3.org/TR/odrl-vocab/#term-Offer) defining the [Usage Policy](../model/terminology.md#policy) associated with the [Dataset](../model/terminology.md#dataset) and this explicit `Distribution`.
[Offers](../model/terminology.md#offer) must NOT contain any target attributes. The target of an [Offer](../model/terminology.md#offer) is the [Dataset](../model/terminology.md#dataset) that contains the distribution.

Support for `hasPolicy` attributes on a `Distribution` is optional. Implementations may choose not to support this feature, in which case they should return an appropriate error
message to clients.

### 3.3 DataService

A DataService may specify an IDS service endpoint such as a [Connector](../model/terminology.md#connector--data-service-).

#### 3.3.1 dspace:dataServiceType

If the DataService refers to an IDS service endpoint, it must include the property `dspace:dataServiceType`:

| Category   | Description                                                                |
|------------|----------------------------------------------------------------------------|
| Definition | Specifies the IDS service type                                             |
| Domain     | [dcat:DataService](https://www.w3.org/TR/vocab-dcat-2/#Class:Data_Service) |
| Type       | xsd:string                                                                 |
| Note       | The value of this field is left intentionally open for future extension.   |

The following table lists well-know IDS endpoint types:

| Value         | Description          |
|---------------|----------------------|
| dspace:connector | A [Connector](../model/terminology.md#connector--data-service-) endpoint |
|               |                      |

#### 3.3.2 dcat:servesDataset

Note that the property `dcat:servesDataset` should be omitted from the `DataService` since [Datasets](../model/terminology.md#dataset) are included as top-level entries. Clients are not required to process the
contents of `dcat:servesDataset`.

## 4 Technical Considerations

### 4.1 Queries and Filter Expressions

A [Catalog Service](../model/terminology.md#catalog-service) may support [Catalog](../model/terminology.md#catalog) queries or filter expressions as an implementation-specific feature. However, it is expected that query capabilities will be implemented
by the [Consumer](../model/terminology.md#consumer) against the results of a `CatalogRequestMessage,` as the latter is an RDF vocabulary. Client-side querying can be scaled by periodically crawling the [Provider's](../model/terminology.md#provider's)
[Catalog Services](../model/terminology.md#catalog-service), caching the results, and executing queries against the locally-stored [Catalogs](../model/terminology.md#catalog).

### 4.2 Replication Protocol

The Catalog Protocol is designed to be used by federated services without the need for a replication protocol. Each [Consumer](../model/terminology.md#consumer) is responsible for issuing requests
to 1..N [Catalog Services](../model/terminology.md#catalog-service), and managing the results. It follows that a specific replication protocol is not needed, or more precisely, each [Consumer](../model/terminology.md#consumer) replicates data from catalog
services by issuing `CatalogRequestMessages`.

The discovery protocol adopted by a particular [Dataspace](../model/terminology.md#dataspace) defines how a [Consumer](../model/terminology.md#consumer) discovers [Catalog Services](../model/terminology.md#catalog-service).

### 4.3 Security

It is expected (although not required) that [Catalog Services](../model/terminology.md#catalog-service) implement access control. A [Catalog](../model/terminology.md#catalog) as well as individual [Datasets](../model/terminology.md#dataset) may be restricted to trusted parties.
The [Catalog Service](../model/terminology.md#catalog-service) may require [Consumers](../model/terminology.md#consumer) to include a security token along with a `CatalogRequestMessage.` The specifics of how this is done can be found in the relevant
Catalog Binding Specification. The semantics of such tokens are not part of this specification.

#### 4.3.1 The Proof Metadata Endpoint

When a [Catalog](../model/terminology.md#catalog) contains protected [Datasets](../model/terminology.md#dataset) the [Provider](../model/terminology.md#provider) has two options: include all [Datasets](../model/terminology.md#dataset) in the [Catalog](../model/terminology.md#catalog) response and restrict access when a contract is negotiated; 
or, require one or more proofs when the [Catalog](../model/terminology.md#catalog) request is made and filter the [Datasets](../model/terminology.md#dataset) accordingly. The latter option requires a mechanism for clients to discover 
the type of proofs that may be presented at request time. The specifics of proof types and presenting a proof during a [Catalog](../model/terminology.md#catalog) request is outside the scope of the 
Dataspace Protocol Specifications. However, Catalog Binding Specifications should define a proof data endpoint for obtaining this information.  

### 4.4 Catalog Brokers

A [Dataspace](../model/terminology.md#dataspace) may include _**catalog brokers**_. A catalog broker is a [Consumer](../model/terminology.md#consumer) that has trusted access to 1..N upstream [Catalog Services](../model/terminology.md#catalog-service) and advertises their respective [Catalogs](../model/terminology.md#catalog) as
a single [Catalog Service](../model/terminology.md#catalog-service). The broker is expected to honor upstream access control requirements.

## 5 DCAT and ODRL Profiles

The [Catalog](../model/terminology.md#catalog) is a [DCAT Catalog](https://www.w3.org/TR/vocab-dcat-3/#Class:Catalog) with the following restrictions:

1. Each [ODRL `Offer`](https://www.w3.org/TR/odrl-vocab/#term-Offer) must be unique to a [Dataset](../model/terminology.md#dataset) since the target of the [Offer](../model/terminology.md#offer) is derived from its enclosing context.
2. Each [ODRL `Offer`](https://www.w3.org/TR/odrl-vocab/#term-Offer) must NOT include an explicit `target` attribute. 
