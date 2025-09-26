# SyncActiveEscortsRequestV1

This message is sent by the Fleet Management System (FMS) to the Autonomous Haulage System (AHS) to provide an out of sync Autonomous Vehicle (AV) with the current complete set of **active** escorts. The AV must activate all escorts within the request before it is permitted to operate.

> [!IMPORTANT]
> The `SyncActiveEscortsRequestV1` message shall not contain any escorts that are pending. Pending escorts shall be communicated separately using the `ActivateEscortRequestV1` message.
---

| Sender | Triggered by | Triggers |
| --- | --- | --- |
| `FMS`  | `OutOfSyncV1` | The AV to adhere to the existing activated escorts, and fire off `SyncAllActiveEscortsResponseV1` messages |

## Message Attributes

The `SyncAllActiveEscortsRequestV1` is

| Key | Value | Format | Required | Description |
| --- | :---: | :---: | :---: | --- |
| `"RequestId"` | RequestId | UUID | True | A unique identifier for the request. This is used to match the request with the response. |
| `"Escorts"` | | Array[Escort] | True |An array of [GeoJSON](https://datatracker.ietf.org/doc/html/rfc7946) object consist in the Escort Object. |

>[!NOTE]
> The top-level message headers should contain the `EquipmentId`, indicating which AV the `SyncAllActiveEscortsRequestV1` message is for.

### Escort Object
The Escort object is a GeoJSON [RFC7946](https://datatracker.ietf.org/doc/html/rfc7946) compatible feature object that describes the escort. It contains the following properties:
| Key | Value | Format | Required | Description |
| --- | :---: | :---: | :---: | --- |
| `"id"` | EscortId | String | True | A unique identifier for the escort |
| `"type"` | `Feature` | String | True | The GeoJSON compatible feature type |
| `"geometry"` | Geometry | Object | True | A GeoJSON compatible geometry object |
| `"properties"` | Properties | Object | True | A GeoJSON compatible properties object |

### Geometry Object
| Key | Value | Format | Required | Description |
| --- | :---: | :---: | :---: | --- |
| `"type"` | `Polygon` | String | True | The geometry type that conforms with GeoJSON geometry `Polygon` |
| `"coordinates"` |  | Array[Array[Array[Number, Number, Number]]] | True | A GeoJSON compatible polygon geometry coordinates. <br/> **NOTE** each coordinate must consist of 3 number, [longitude, latitude, elevation]. See [GeoJSON Geometry Object](https://datatracker.ietf.org/doc/html/rfc7946#section-3.1) |


### Properties Object
| Key | Value | Format | Required | Description |
| --- | :---: | :---: | :---: | --- |
| `"id"` | EscortId | String | True | The escort id |
| `"name"` |  | String | True | The name of the escort |


## Examples
### Typical Message
```JSON
{
  "Protocol": "Open-Autonomy",
  "Version": 1,
  "Timestamp": "2024-08-23T08:19:56.631Z",
  "EscorterId": "11111111-2222-3333-4444-555555555555",
  "EquipmentId": "e4de3723-a315-4506-b4e9-537088a0eabf",
  "SyncActiveEscortsRequestV1": {
    "RequestId": "00000000-0000-0000-0000-000000000001",
    "Escorts": [
        { },
        "id": "00000000-0000-0000-0000-000000000001",
        "properties": {
            "name": "grading 1",
        }
    ]
  }
}
```