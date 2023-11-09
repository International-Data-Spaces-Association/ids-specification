# Catalog HTTPS Binding

## 1 Introduction

This specification defines a RESTful API over HTTPS for the [Catalog Protocol].

The OpenAPI definitions for this specification can be accessed [here](TBD).

## 2 Path Bindings

### 2.1 Prerequisites

1. The `<base>` notation indicates the base URL for a catalog service endpoint. For example, if the base catalog URL is `api.example.com`, the URL `https://<base>/catalog/request`
   will map to `https//api.example.com/catalog/request`.

2. All request and response messages must use the `application/json` media type.

### 2.2 CatalogError

In the event of a request error, the catalog service must return an appropriate HTTP code and a [CatalogError](./catalog.protocol.md#) in the response body.

| Field   | Type          | Description                                                 |
|---------|---------------|-------------------------------------------------------------|
| code    | string        | An optional implementation-specific error code.             |
| reasons | Array[object] | An optional array of implementation-specific error objects. |

### 2.3 The `catalog/request` endpoint

#### 2.3.1 POST

The [CatalogRequestMessage](catalog.protocol.md#21-catalogrequestmessage) corresponds to `POST https://<base>/catalog/request`:

```
POST https://provider.com/catalog/request

Authorization: ...

{
  "@context":  "https://w3id.org/dspace/v0.8/context.json",
  "@type": "dspace:CatalogRequestMessage",
  "dspace:filter": {}
}
```

The `Authorization` header is optional if the catalog service does not require authorization. If present, the contents of the `Authorization` header are detailed in the
[Authorization section](#31-authorization).

The `filter` property is optional. If present, the `filter` property can contain an implementation-specific filter expression or query to be executed as part of the catalog
request.

#### 2.3.2 OK (200) Response

If the request is successful, the catalog service must return a response body containing a [Catalog](./message/catalog.json) which is a profiled DCAT `Catalog` type
described by the [Catalog Protocol Specification](catalog.protocol.md).

### 2.4 The `catalog/datasets/{id}` endpoint

#### 2.3.1 GET

The [DatasetRequestMessage](catalog.protocol.md#24-datasetrequestmessage) corresponds to `GET https://<base>/catalog/datasets/{id}}`:

```
GET https://provider.com/catalog/datasets/{id}

Authorization: ...

```

The `Authorization` header is optional if the catalog service does not require authorization. If present, the contents of the `Authorization` header are detailed in the
[Authorization section](#31-authorization).

#### 2.4.2 OK (200) Response

If the request is successful, the catalog service must return a response body containing a [Dataset](./message/dataset.json) which is a DCAT `Dataset` type
described by the [Catalog Protocol Specification](catalog.protocol.md).

## 3 Technical Considerations

### 3.1 Authorization

A catalog service may require authorization. If the catalog service requires authorization, requests must include an HTTP `Authorization` header with a token. The semantics of
such tokens are not part of this specification.

### 3.2 Versioning

- Versioning will be done via URLs. TBD.

### 3.3 Pagination

A catalog service may paginate the results of a `CatalogRequestMessage`. Pagination data is specified using [Web Linking](https://datatracker.ietf.org/doc/html/rfc5988)
and the HTTP `Link` header. The `Link` header will contain URLs for navigating to previous and subsequent results. The following request sequence demonstrates pagination:

```
Link: <https://provider.com/catalog?page=2&per_page=100>; rel="next"
{
  "@context":  "https://w3id.org/dspace/v0.8/context.json",
  "@type": "dcat:Catalog"
  ...
}

```

Second page response:

```
Link: <https://provider.com/catalog?page=1&per_page=100>; rel="previous"
Link: <https://provider.com/catalog?page=3&per_page=100>; rel="next"

{
   "@type": "dcat:Catalog"
   ...
}
```

Last page response:

```
Link: <https://provider.com/catalog?page=2&per_page=100>; rel="previous"

{
   "@type": "dcat:Catalog"
   ...
}
```

### 3.4 Compression

Catalog services MAY compress responses to a catalog request by setting the `Content-Encoding` header to `gzip` as described in
the [HTTP 1.1 Specification](https://www.rfc-editor.org/rfc/rfc9110.html#name-gzip-coding).

## 4 The Well-Known Proof Metadata Endpoint

When an implementation supports protected _datasets_, it may offer a proof metadata endpoint clients can use to determine proof requirements. If the implementation
offers a proof data endpoint, it must use the `dspace-trust` [Well-Known Uniform Resource Identifier](https://www.rfc-editor.org/rfc/rfc8615.html) at the top of the 
path hierarchy:

`/.well-known/dspace-trust`

The contents of the response is a JSON object defined by individual trust specifications and not defined here.

Note that if multiple connectors are hosted under the same base URL, a path segment appended to the base well-known URL can be used, for example,
`https://example.com/.well-known/dspace-trust/connector1.`

## 5 Notes

### 5.1 Asynchronous Interactions

We may want to specify optional support for asynchronous callbacks for the catalog response. This would require adding a `callbackAddress` property and an `@id` to the request:

```
POST https://provider.com/catalog/request

Authorization: ...

{
  "@context":  "https://w3id.org/dspace/v0.8/context.json",
  "@type": "dspace:CatalogRequestMessage"
  "@id: "..."
  "dspace:callbackAddress": "https://example.com/endpoint"
}
```

The `CatalogResponseMessage` would be POSTed back to the endpoint. the response message could be posted multiple times for paginated results and would need to include the
original `@id` value as a `correlationId` and a property indicating if the contents are complete (or additional responses will be sent).
