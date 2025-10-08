# ActivateEscortRequestV1

This message is sent by the Fleet Management System (FMS) to the Autonomous Haulage System (AHS) to indicate a escort has been created in the FMS which the Autonomous Vehicles (AV) are expected to adhere to. Each AV should then respond with an `ActivateEscortResponseV1` message indicating whether it has accepted, activated or rejected the escort request (see `ActivateEscortResponseV1` for a description of response types).

| Sender | Triggered by | Triggers |
| --- | --- | --- |
| `FMS`  | Escort creation or updates | The AV to start accepting and adher to the escorts, and fire off `ActivateEscortResponseV1` messages |

## Message Attributes

The `ActivateEscortResponseV1` message consists of the following properties.

| Key | Value | Format | Required | Description |
| --- | :---: | :---: | :---: | --- |
| `"Escorts"` | - | Array[Escort] | True | A single Escort object is described below. |

>[!NOTE]
> The top-level message headers should contain the `EquipmentId`, indicating which AV the `ActivateEscortRequestV1` message is for.
### Escort Object


| Key | Value | Format | Required | Description |
| --- | :---: | :---: | :---: | --- |
| `"EscorterId"` | UUID | UUID | False | Identifier of the escorting vehicle providing protection. Required if the `Escorts` list in `ActivateEscortResponseV1` is of length greater than 1 |
| `"EscortId"` | UUID | UUID | True | A unique identifier for the escort operation |
| `"V2XStationId"` | StationId  | integer | True | V2X StationId of the Escorter SIV |
| `"Length"` | m | decimal | True | Length of protection Zone |
| `"Width"` | m | decimal | True | Width of protection Zone, applicable when Escort Convoy is in open area. |

> [!NOTE]
> Why V2X StationId?
> - V2X CAM messages can be received with smaller latency than the `EscortPositionV1` messages therefore StationId of the Escorter is a valuable information to the AV.
> 
> Usually V2X StationIds are changing from time to time. The Escorter must not change its StationId during the extent of the specific escort operation.


## Examples
### Typical Message
```JSON
{
    "Protocol": "Open-Autonomy",
    "Version": 1,
    "Timestamp": "2021-09-01T12:00:00Z",
    "EscorterId": "11111111-2222-3333-4444-555555555555",
    "EquipmentId": "e4de3723-a315-4506-b4e9-537088a0eabf",
    "ActivateEscortRequestV1": {
        "Escorts": [
            {
                "EscorterId": "11111111-2222-3333-4444-555555555555",
                "EscortId": "00000000-0000-0000-0000-000000000001",
                "StationId": 23983958,
                "Length": 200.0,
                "Width": 6.0
            }
        ]
    }
}
```