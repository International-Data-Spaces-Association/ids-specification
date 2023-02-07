# International Dataspaces (IDS) - Version X

### Working Draft 1 February 2023

> __NOTE:__ These specifications are working drafts and subject to change.

## Abstract

IDS is a set of specifications designed to facilitate interoperable data sharing between entities governed by usage control and based on Web technologies. These specifications
define the schemas and protocols required for entities to publish data, negotiate usage agreements, and access data as part of a federation of technical systems termed a
__*dataspace*__.

## Introduction

Sharing data between autonomous entities requires the provision of metadata to facilitate the transfer of assets over a particular wire (or application layer) protocol.
IDS defines how this metadata is provisioned:

1. How data assets are deployed as [DCAT Catalogs](https://www.w3.org/TR/vocab-dcat-3/) and usage control is expressed as [ODRL Policies](https://www.w3.org/TR/odrl-model/).
2. How contract agreements that govern data access are syntactically expressed and electronically negotiated.
3. How data assets are accessed using wire protocols.

These specifications do not define wire protocols for data transfer. Instead, these specifications are designed to work with existing and future wire protocols that facilitate
moving data between autonomous systems.

The specifications are organized into the following documents:

* A __*Dataspace Information Model*__ document that defines key terms.
* __*Catalog Protocol*__ and __*Catalog HTTPS Binding*__ documents that define how DCAT Catalogs are published and accessed as HTTPS endpoints respectively.
* __*Contract Negotiation Protocol*__ and __*Contract Negotiation HTTPS Binding*__ documents that define how contract negotiations are conducted and requested via HTTPS endpoints.
* __*Transfer Process Protocol*__ and __*Transfer Process HTTPS Binding*__ documents that define how transfer processes using a given wire protocol are governed via HTTPS
  endpoints.



