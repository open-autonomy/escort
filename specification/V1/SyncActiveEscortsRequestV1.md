# SyncActiveEscortsRequestV1

This message is sent by the Fleet Management System (FMS) to the Autonomous Haulage System (AHS) to provide an out of sync Autonomous Vehicle (AV) with the current complete set of **active** escorts. The AV must activate all escorts within the request before it is permitted to operate.

> [!IMPORTANT]
> The `SyncActiveEscortsRequestV1` message shall not contain any escorts that are pending. Pending escorts shall be communicated separately using the `ActivateEscortRequestV1` message.
---

| Sender | Triggered by | Triggers |
| --- | --- | --- |
| `FMS`  | `OutOfSyncV1` | Causes AV to activate all listed escorts; AV answers with `SyncActiveEscortsResponseV1` |

## Message Attributes

The `SyncActiveEscortsRequestV1` payload contains:

| Key | Value | Format | Required | Description |
| --- | :---: | :---: | :---: | --- |
| `"RequestId"` | RequestId | UUID | True | A unique identifier for the request. This is used to match the request with the response. |
| `"Escorts"` | | Array[ActivateEscortRequestV1] | True | Array of active escort definitions (no Pending escorts). |

>[!NOTE]
> The top-level message header SHALL contain `EquipmentId` identifying the single AV this synchronization request targets.


## Examples
### Typical Message
```json
{
  "Protocol": "Open-Autonomy",
  "Version": 1,
  "Timestamp": "2024-08-23T08:19:56.631Z",
  "EquipmentId": "f0c3d5ab-2d6e-4a12-b9d9-9eaf1efc0abc",
  "SyncActiveEscortsRequestV1": {
    "RequestId": "00000000-0000-0000-0000-000000000001",
    "Escorts": [
      {
        "EscorterId": "11111111-2222-3333-4444-555555555555",
        "EscortId": "00000000-0000-0000-0000-000000000001",
        "Length": 100.0,
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
      },
      {
        "EscorterId": "22222222-1111-3333-4444-555555555555",
        "EscortId": "00000000-0000-0000-0000-000000000002",
        "Length": 200.0,
        "Width": 6.0,
        "OnRoadSpeedLimit": 15.0,
        "EscortPositionUpdate": {
          "EscorterId": "22222222-1111-3333-4444-555555555555",
          "GpsWeek": 2444,
          "GpsMilliSecondInWeek": 345678900,
          "V2XStationId": 19839348,
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
    ]
  }
}
```
