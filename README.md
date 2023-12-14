# Dataspace Protocol - Working Draft

> __NOTE:__ These specifications are working drafts and subject to change.

> __NOTE:__ For GitHub users, The link to the rendered content is https://docs.internationaldataspaces.org/dataspace-protocol/

> __NOTE:__ The human-friendly version of this specification in the
> [IDSA Knowledge base](https://docs.internationaldataspaces.org/dataspace-protocol/)
> will always show the latest version of the document.
> The version history and changes are provided via the [GitHub Repository](https://github.com/International-Data-Spaces-Association/ids-specification/).

## About versions of the Dataspace Protocol

The specification of the Dataspace Protocol is work in progress and subject to change.
Since [version 0.8](https://github.com/International-Data-Spaces-Association/ids-specification/releases/tag/v0.8) the specification is stable with changes in details.
All changes made to the specification can be reviewed in the [GitHub repository](https://github.com/International-Data-Spaces-Association/ids-specification/).

> __NOTE:__ A versioning scheme beside the commits to the repository is not available but will be provided in the future. 

## Abstract

The __Dataspace Protocol__ is a set of specifications designed to facilitate interoperable data sharing between entities governed by usage control and based on Web technologies. These specifications
define the schemas and protocols required for entities to publish data, negotiate usage agreements, and access data as part of a federation of technical systems termed a
__*dataspace*__.

## Introduction

Sharing data between autonomous entities requires the provision of metadata to facilitate the transfer of datasets by making use of a data transfer (or application layer) protocol.
The __Dataspace Protocol__ defines how this metadata is provisioned:

1. How data datasets are deployed as [DCAT Catalogs](https://www.w3.org/TR/vocab-dcat-3/) and usage control is expressed as [ODRL Policies](https://www.w3.org/TR/odrl-model/).
2. How contract agreements that govern data usage are syntactically expressed and electronically negotiated.
3. How datasets are accessed using data transfer protocols.

These specifications build on protocols located in the [ISO OSI   model (ISO/IEC 7498-1:1994)](https://www.iso.org/standard/20269.html) layers, like HTTPS.
The purpose of this specification is to define interactions between systems independent of such protocols, but describing how to implement it in an unambiguous and extensible way.
To do so, the messages that are exchanged during the process are described in this specification and the states and their transitions are specified as state machines, based on the key terms and concepts of a data space.
On this foundation the binding to data transfer protocols, like HTTPS, is described.

The specifications are organized into the following documents:

* __*Dataspace Model*__ and __*Dataspace Terminology*__ documents that define key terms.
* __*Catalog Protocol*__ and __*Catalog HTTPS Binding*__ documents that define how DCAT Catalogs are published and accessed as HTTPS endpoints respectively.
* __*Contract Negotiation Protocol*__ and __*Contract Negotiation HTTPS Binding*__ documents that define how contract negotiations are conducted and requested via HTTPS endpoints.
* __*Transfer Process Protocol*__ and __*Transfer Process HTTPS Binding*__ documents that define how transfer processes using a given data transfer protocol are governed via HTTPS
  endpoints.

This specification does not cover the data transfer process as such.
While the data transfer is controlled by the __*Transfer Process Protocol*__ mentioned above, the data transfer itself and especially the handling of technical exceptions is an obligation to the Transport Protocol.
As an implication, the data transfer can be conducted in a separated process if required, as long as this process is to the specified extend controlled by the __*Transfer Process Protocol*__.

### Context of this specification

The __Dataspace Protocol__ is used in the context of data spaces as described and defined in the subsequent sections with the purpose to support interoperability.
In this context, the specification provides fundamental technical interoperability for participants in data spaces and therefore the protocol specified here is required to join any data space as specified [here]().
Beyond the technical interoperability measures described in this specification, semantic interoperability should also be addressed by the participants. On the perspective of the data space, interoperability needs to be addressed also on the level of trust, on organizational level and on legal level.
The aspect of cross data space communication is not subject of this document, as this is addressed by the data spaces' organizational and legal agreements.

The interaction of participants in a data space is conducted by the participant agents, so-called Connectors, which implement the protocols described above.
While most interactions take place between Connectors, some interactions with other systems are required.
The figure below provides an overview on the context of this specification.

An Identity Provider realizes the required interfaces and provides required information to implement Trust Framework of a data space.
The validation of the identity of a given participant agent and the validation of additional claims is the fundamental mechanism. The structure and content of such claims and identity may vary between different data spaces, as well as the structure of such an Identity Provider, e.g. a centralized system, a decentralized system or a federated system.

A connector will implement additional internal functionalities, like monitoring or Policy Engines, as appropriate. It is not covered by this specification, if a connector implements such or how.

The same applies for the data, which is transferred between the systems. While this document does not define the transport protocol, the structure, syntax and semantics of the data, a specification for those aspects is required and subject to the agreements of the participants or the data space.

![Overview on protocol and context](./resources/figures/ProtocolOverview.png)

## Best Practices

The Dataspace Protocol is under development and the working group is active on this draft, reviewed and improved the content multiple times. During the process several aspects were discussed, which are not considered part of the normative specification, but important to be documented as support for the users of this specification as best practices. The [Best Practices](./best.practices/README.md) are non-normative.

Users of this specification are invited to provide feedback such as, but not limited to:

* What information is missing?
* What information, including examples, would you like to see?
* What did you like in this document?

Please provide your feedback as Issue in our [GitHub repository](https://github.com/International-Data-Spaces-Association/ids-specification/issues).

