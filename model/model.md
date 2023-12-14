# 2 Dataspace Model

## 2 Dataspace Information Model

The following sections outline the Dataspace Information Model, which form the foundation of this specification.

### 2.1 Dataspace Entity Relationships

The relationships between the primary [Dataspace](./terminology.md#dataspace) entities are defined as follows:

![](./m.dataspace.relationships.png)

Note that all relationships are multiplicities unless specified.

- A [Dataspace Authority](./terminology.md#dataspace-authority) manages one or more [Dataspaces](./terminology.md#dataspace). This will include [Participant](./terminology.md#participant) registration and may entail mandating business and/or requirements. For example, a
  [Dataspace Authority](./terminology.md#dataspace-authority) may require [Participants](./terminology.md#participant) to obtain some form of business certification. A [Dataspace Authority](./terminology.md#dataspace-authority) may also impose technical requirements such as support for the
  technical enforcement of specific usage policies.
- A [Participant](./terminology.md#participant) is a member of one or more [Dataspaces](./terminology.md#dataspace). A [Participant](./terminology.md#participant) registers [Participant Agents](./terminology.md#participant-agent) that perform tasks on its behalf.
- A [Participant Agents](./terminology.md#participant-agent) performs tasks such as publishing a catalog or engaging in a [Dataset](../model/terminology.md#dataset) transfer. In order to accomplish these tasks, a [Participant Agents](./terminology.md#participant-agent) may
  use a _**verifiable presentation**_ generated from a _**credential**_ obtained from a third-party [Credential Issuer](./terminology.md#credential-issuer). A [Participant Agents](./terminology.md#participant-agent) may also use an _**ID token**_ issued by a
  third-party [Identity Provider](./terminology.md#identity-provider). Note that a [Participant Agents](./terminology.md#participant-agent) is a logical construct and does not necessarily correspond to a single runtime process.
- An [Identity Provider](./terminology.md#identity-provider) is a trust anchor that generates `ID tokens` used to verify the identity of a [Participant Agents](./terminology.md#participant-agent). Multiple identity providers may operate in
  a [Dataspace](./terminology.md#dataspace). The types and semantics of ID tokens are not part of this specification. An [Identity Provider](./terminology.md#identity-provider) may be a third-party or a [Participant](./terminology.md#participant) itself (for example, in the case
  of decentralized identifiers).
- A [Credential Issuer](./terminology.md#credential-issuer) issues _verifiable credentials_ used by [Participant Agents](./terminology.md#participant-agent) to allow access to [Datasets](../model/terminology.md#dataset) and verify usage control.

The diagram below depicts the relationships between [Participant Agent](./terminology.md#participant-agent) types:

![](./m.participant.entities.png)

- A `CatalogService` is a [Participant Agent](./terminology.md#participant-agent) that makes a [DCAT Catalog](https://www.w3.org/TR/vocab-dcat-3/#Class:Catalog) available to other [Participants](./terminology.md#participant).
- A `Catalog` contains one or more [Datasets](../model/terminology.md#dataset), which are [DCAT Datasets](https://www.w3.org/TR/vocab-dcat-3/#Class:Dataset). A `Catalog` also contains **_at least one_**
  [DCAT DataService](https://www.w3.org/TR/vocab-dcat-3/#Class:Data_Service) that references a `Connector` where [Datasets](../model/terminology.md#dataset) may be obtained.
- A [Dataset](../model/terminology.md#dataset) has **_at least one_** `Offer`, which is an [ODRL Offer](https://www.w3.org/TR/odrl-model/#policy-offer) describing the [Usage Policy](../model/terminology.md#policy) associated with the [Dataset](../model/terminology.md#dataset).
- A `Connector` is a [Participant Agent](./terminology.md#participant-agent) that performs `Contract Negotiation` and `Transfer Process` operations with another connector. An outcome of a `ContractNegotiation` may
  be the production of an `Agreement`, which is an [ODRL Agreement](https://www.w3.org/TR/odrl-model/#policy-agreement) defining the [Usage Policy](../model/terminology.md#policy) agreed to for a [Dataset](../model/terminology.md#dataset).

## 2.2 Classes

Not all [Dataspace](./terminology.md#dataspace) entities have a concrete _technical_ materialization; some entities may exist as purely logical constructs. For example, a [Dataspace Authority](./terminology.md#dataspace-authority)
and [Participant Agent](./terminology.md#participant-agent) have no representation in the protocol message flows that constitute [Dataspace](./terminology.md#dataspace) interactions. This section outlines the classes that comprise the concrete
elements of the model, i.e. those that are represented in protocol message flows.

**_Note 1:_**
The classes and definitions used in the Dataspace Protocol are reused from different standards and specifications as much as possible, in particular, DCAT and ODRL. As, however, the external definitions allow different interpretations or provide more attributes than required, the dataspace protocol is leveraging _profiles_ of the original definitions rather than the complete original expressiveness. A _profile_ in this sense is a restriction or subset of an external definition, enforcing that every occurance of an externally defined class is always conformant with the original definition. However, not every standard-compliant class might be compliant to the dataspace profile.

### 2.2.1 Catalog

A `Catalog` is a [DCAT Catalog](https://www.w3.org/TR/vocab-dcat-3/#Class:Catalog) with the following attributes:

- 0..N [Datasets](../model/terminology.md#dataset). Since a catalog may be dynamically generated for a request based on the requesting [Participant's](./terminology.md#participant) credentials it is possible for it to contain 0 matching
  [Datasets](../model/terminology.md#dataset). (DCAT PROFILE)
- 1..N [DCAT DataService](https://www.w3.org/TR/vocab-dcat-3/#Class:Data_Service) that references a `Connector` where [Datasets](../model/terminology.md#dataset) may be obtained. (DCAT PROFILE)

### 2.2.2 Dataset

A [Dataset](../model/terminology.md#dataset) is a [DCAT Dataset](https://www.w3.org/TR/vocab-dcat-3/#Class:Dataset) with the following attributes:

- 1..N `hasPolicy` attributes that contain an ODRL `Offer` defining the [Usage Policy](../model/terminology.md#policy) associated with the [Dataset](../model/terminology.md#dataset). **_Offers must NOT contain any target attributes. The
  target of an offer is the associated [Dataset](../model/terminology.md#dataset)._** (ODRL PROFILE)
- 1..N [DCAT Distributions](https://www.w3.org/TR/vocab-dcat-3/#Class:Distribution). Each distribution must have at least one `DataService` which specifies where the distribution
  is obtained. Specifically, a `DataService` specifies the endpoint for initiating a `ContractNegotiation` and `TransferProcess`. (DCAT PROFILE)

### 2.2.3 Offer

An `Offer` is an [ODRL Offer](https://www.w3.org/TR/odrl-model/#policy-offer) with the following attributes:

- An ODRL `uid` is represented as an "@id" that is a unique UUID. (ODRL PROFILE)
- The `Offer` must be unique to a [Dataset](../model/terminology.md#dataset) since the target of the offer is derived from its enclosing context.
- The `Offer` must NOT include an explicit `target` attribute.

## 2.2.4 Agreement

An `Agreement` is an [ODRL Agreement](https://www.w3.org/TR/odrl-model/#policy-agreement) with the following attributes:

- The `Agreement` class must include one `target` attribute that is the UUID of the [Dataset](../model/terminology.md#dataset) the agreement is associated with. An agreement is therefore associated with **EXACTLY
  ONE** [Dataset](../model/terminology.md#dataset). (ODRL PROFILE)
