# ActivateEscortRequestV1

This message is sent by the Fleet Management System (FMS) to the Autonomous Haulage System (AHS) to announce an escort instance that Autonomous Vehicles (AVs) SHALL acknowledge and enforce. Each AV SHALL respond with an [ActivateEscortResponseV1](/specification/V1/ActivateEscortResponseV1.md) indicating one of: Pending, Activated, or Rejected (see [ActivateEscortResponseV1 Status Semantics](/specification/V1/ActivateEscortResponseV1.md/#status-semantics)).

| Sender | Triggered by | Triggers |
| --- | --- | --- |
| FMS | Successful escort creation (immutable parameters established) | An [ActivateEscortResponseV1](/specification/V1/ActivateEscortResponseV1.md) from each addressed AV |

## Message Attributes

The `ActivateEscortRequestV1` message consists of the following properties.

| Key | Value | Format | Required | Description |
| --- | :---: | :---: | :---: | --- |
| `"EscorterId"` | UUID | String | Shall | Identifier of the Escorter Vehicle |
| `"EscortId"` | UUID | String | Shall | A unique identifier for the escort operation |
| `"Length"` | m | double | Shall | Length of protection Zone |
| `"Width"` | m | double | Shall | Lateral Protection Zone extent used in open areas. Lane boundaries supersede Width on roads. |
| `"OnRoadSpeedLimit"` | m/s | double | Shall | This speed limit is applicable when the Escort drives on roads. The escort must never violate this speed limit. |
| `"OpenAreaSpeedLimit"` | m/s | double | Shall | This speed limit is applicable when the Escort drives in open areas. The escort must never violate this speed limit. |
| `"EscortPositionUpdateV1"` | [EscortPositionUpdateV1](/specification/V1/EscortPositionUpdateV1.md) | Object | Shall | Position snapshot used to seed AV prediction and Avoidance Zone calculation. |

>[!IMPORTANT]
> The AV will reject the request if the escort position is in some way faulty

>[!IMPORTANT]
> The AV will use the speed limits when increasing the avoidance zone in accordance to the time delay since last [EscortPositionUpdateV1](/specification/V1/EscortPositionUpdateV1.md).

>[!WARNING]
> If the Escort violates the speed limits the Escort may end up outside of the AVs' avoidance zones.


## Example
```json
{
  "Protocol": "Open-Autonomy",
  "Version": 1,
  "Timestamp": "2025-10-20T09:30:10.435Z",
  "EquipmentId": "f0c3d5ab-2d6e-4a12-b9d9-9eaf1efc0abc",
  "ActivateEscortRequestV1": {
    "EscorterId": "11111111-2222-3333-4444-555555555555",
    "EscortId": "00000000-0000-0000-0000-000000000001",
    "Length": 200.0,
    "Width": 6.0,
    "OnRoadSpeedLimit": 10.0,
    "OpenAreaSpeedLimit": 6.0,
    "EscortPositionUpdateV1": {...}
  }
}
```


