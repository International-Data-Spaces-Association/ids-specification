# Catalog HTTPS Binding

This specification defines a RESTful API over HTTPS for the [Catalog Protocol](./catalog.protocol.md).

## 1 Introduction

### 1.1 Prerequisites

1. The `<base>` notation indicates the base URL for a [Catalog Service](../model/terminology.md#catalog-service) endpoint. For example, if the base [Catalog](../model/terminology.md#catalog) URL is `api.example.com`, the URL `https://<base>/catalog/request` will map to `https//api.example.com/catalog/request`.

2. All request and response messages must use the `application/json` media type.

### 1.2 Catalog Error

In the event of a request error, the [Catalog Service](../model/terminology.md#catalog-service) must return an appropriate HTTP code and a [Catalog Error](./catalog.protocol.md#43-error---catalog-error) in the response body.

### 1.3 Authorization

A [Catalog Service](../model/terminology.md#catalog-service) may require authorization. If the [Catalog Service](../model/terminology.md#catalog-service) requires authorization, requests must include an HTTP `Authorization` header with a token. The semantics of such tokens are not part of this specification.

## 2 Path Bindings

| Endpoint                                  | Method | Description                |
|:------------------------------------------|:-------|:---------------------------|
| https://provider.com/catalog/request      | `POST` | Section [2.1.1](#211-post) |
| https://provider.com/catalog/datasets/:id | `GET`  | Section [2.2.1](#221-get)  |

### 2.1 The `catalog/request` Endpoint (Provider-side)

#### 2.1.1 POST

##### Request

The [Catalog Request Message](./catalog.protocol.md#31-catalog-request-message) corresponds to `POST https://<base>/catalog/request`:

```http request
POST https://provider.com/catalog/request

Authorization: ...

{
  "@context":  "https://w3id.org/dspace/v0.8/context.json",
  "@type": "dspace:CatalogRequestMessage",
  "dspace:filter": {}
}
```

- The `Authorization` header is optional if the [Catalog Service](../model/terminology.md#catalog-service) does not require authorization. If present, the contents of the `Authorization` header are detailed in the [Authorization section](#13-authorization).

- The `filter` property is optional. If present, the `filter` property can contain an implementation-specific filter expression or query to be executed as part of the [Catalog](../model/terminology.md#catalog) request.

##### Response

If the request is successful, the [Catalog Service](../model/terminology.md#catalog-service) must return an HTTP 200 (OK) with a response body containing a [Catalog](./catalog.protocol.md#41-ack---catalog) (which is a profiled [DCAT Catalog](https://www.w3.org/TR/vocab-dcat-3/#Class:Catalog) type described by the [Catalog Protocol](catalog.protocol.md)).

### 2.2 The `catalog/datasets/:id` Endpoint (Provider-side)

#### 2.2.1 GET

##### Request

The [Dataset Request Message](./catalog.protocol.md#32-dataset-request-message) corresponds to `GET https://<base>/catalog/datasets/:id}`:

```http request
GET https://provider.com/catalog/datasets/{id}

Authorization: ...

{
  "@context":  "https://w3id.org/dspace/v0.8/context.json",
  "@type": "dspace:DatasetRequestMessage",
  "dspace:dataset": "urn:uuid:3dd1add8-4d2d-569e-d634-8394a8836a88"
}
```

- The `Authorization` header is optional if the [Catalog Service](../model/terminology.md#catalog-service) does not require authorization. If present, the contents of the `Authorization` header are detailed in the [Authorization section](#13-authorization).

##### Response

If the request is successful, the [Catalog Service](../model/terminology.md#catalog-service) must return an HTTP 200 (OK) with a response body containing a [Dataset](./message/example/dataset.json) (which is a [DCAT Dataset](https://www.w3.org/TR/vocab-dcat-3/#Class:Dataset) type described by the [Catalog Protocol](catalog.protocol.md)).

## 3 Technical Considerations

### 3.1 Versioning

- Versioning will be done via URLs. TBD.

### 3.2 Pagination

A [Catalog Service](../model/terminology.md#catalog-service) may paginate the results of a [Catalog Request Message](./catalog.protocol.md#31-catalog-request-message). Pagination data is specified using [Web Linking](https://datatracker.ietf.org/doc/html/rfc5988) and the HTTP `Link` header. The `Link` header will contain URLs for navigating to previous and subsequent results. The following request sequence demonstrates pagination:

```http request
Link: <https://provider.com/catalog?page=2&per_page=100>; rel="next"

{
  "@context":  "https://w3id.org/dspace/v0.8/context.json",
  "@type": "dcat:Catalog",
  ...
}
```

Second page response:

```http request
Link: <https://provider.com/catalog?page=1&per_page=100>; rel="previous"
Link: <https://provider.com/catalog?page=3&per_page=100>; rel="next"

{
   "@type": "dcat:Catalog",
   ...
}
```

Last page response:

```http request
Link: <https://provider.com/catalog?page=2&per_page=100>; rel="previous"

{
   "@type": "dcat:Catalog",
   ...
}
```

### 3.3 Compression

[Catalog Services](../model/terminology.md#catalog-service) MAY compress responses to a [Catalog Request](./catalog.protocol.md#31-catalog-request-message) by setting the `Content-Encoding` header to `gzip` as described in the [HTTP 1.1 Specification](https://www.rfc-editor.org/rfc/rfc9110.html#name-gzip-coding).

## 4 The Well-Known Proof Metadata Endpoint

When an implementation supports protected [Datasets](../model/terminology.md#dataset), it may offer a proof metadata endpoint clients can use to determine proof requirements. If the implementation offers a proof data endpoint, it must use the `dspace-trust` [Well-Known Uniform Resource Identifier](https://www.rfc-editor.org/rfc/rfc8615.html) at the top of the  path hierarchy:

```
/.well-known/dspace-trust
```

The contents of the response is a JSON object defined by individual trust specifications and not defined here.

Note that if multiple [Connectors](../model/terminology.md#connector--data-service-) are hosted under the same base URL, a path segment appended to the base well-known URL can be used, for example, `https://example.com/.well-known/dspace-trust/connector1.`
