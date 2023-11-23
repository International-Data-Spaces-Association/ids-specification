# General Requirements

## Exposure of Dataspace Protocol Versions

Connectors implementing the Dataspace Protocol might operate on different versions. Therefore, it is necessary that they can discover the supported versions of each other in reliable and unambiguous manner. Each Connector must expose information of at least one Dataspace Protocol Version that it supports. The method expose this information is defined through the protocol bindings and might vary amongst them.

In any way, a Connector must respond to a respective request by providing a JSON-LD object containing an array of supported versions with at least one item. The item connects the version tag (`version` attribute) with the absolute URL path segment of the root path for all endpoints of this version. The following example specifies that this Connector offers version `1.0` endpoints at `<host>/some/path/v1`.

```
{
    "@context": "https://w3id.org/dspace/v0.8/context.json",
    "protocolVersions": [
       {
            "version": "1.0",
            "path": "/some/path/v1" 
       }
   ]
}
```

This data object must comply to the [JSON Schema](schema/version-schema.json) and the [SHACL Shape](shape/version-shape.ttl).

The requesting Connector, the one that has received the message with the protocol versions, then must select the according endpoints of the sending Connector as well as configure its own client socket accordingly to the selected version. If the Connector can't identify a matching Dataspace Protocol Version, it must terminate the communication. 