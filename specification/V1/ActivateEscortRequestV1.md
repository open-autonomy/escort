# ActivateEscortRequestV1

This message is sent by the Fleet Management System (FMS) to the Autonomous Haulage System (AHS) to announce an escort instance that Autonomous Vehicles (AVs) SHALL acknowledge and enforce. Each AV SHALL respond with an `ActivateEscortResponseV1` indicating one of: Pending, Active, or Rejected (see `ActivateEscortResponseV1`).

| Sender | Triggered by | Triggers |
| --- | --- | --- |
| FMS | Successful escort creation (immutable parameters established) | An `ActivateEscortResponseV1` from each addressed AV |

## Message Attributes

The `ActivateEscortRequestV1` message consists of the following properties.

| Key | Value | Format | Required | Description |
| --- | :---: | :---: | :---: | --- |
| `"EscorterId"` | UUID | UUID | False | Identifier of the Escorter Vehicle |
| `"EscortId"` | UUID | UUID | True | A unique identifier for the escort operation |
| `"Length"` | m | double | True | Length of protection Zone |
| `"Width"` | m | double | True | Lateral Protection Zone extent used in open areas. Lane boundaries supersede Width on roads. |
| `"OnRoadSpeedLimit"` | m/s | double | True | The Escort must never drive faster than this limit. This limit is applicable to roads. For open areas other speed limits apply. |
| `"EscortPositionUpdate"` | EscortPositionUpdateV1 | EscortPositionUpdateV1 | True | Position snapshot used to seed AV prediction and Avoidance Zone calculation. |

>[!IMPORTANT]
> The AV will reject the request if the escort position is in some way faulty

>[!IMPORTANT]
> The AV will use the speed limit when increasing the avoidance zone in accordance to the time delay since last EscortPositionUpdate.


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
    "EscortPositionUpdate": {
      "EscorterId": "11111111-2222-3333-4444-555555555555",
      "GpsWeek": 2444,
      "GpsMilliSecondInWeek": 345678900,
      "V2XStationId": 23983958,
      "Latitude": 59.1546127,
      "Longitude": 17.6212361,
      "Elevation": 428.32,
      "Heading": 87.8,
      "Speed": 4.2,
      "LatitudeAccuracy": 0.8,
      "LongitudeAccuracy": 0.9,
      "ElevationAccuracy": 1.5,
      "HeadingAccuracy": 2.0,
      "SpeedAccuracy": 0.2
    }
  }
}
```
