# ActivateEscortRequestV1

This message is sent by the Fleet Management System (FMS) to the Autonomous Haulage System (AHS) to indicate a escort has been created in the FMS which the Autonomous Vehicles (AV) are expected to adhere to. Each AV should then respond with an `ActivateEscortResponseV1` message indicating whether it has pending, activated or rejected the escort request (see `ActivateEscortResponseV1` for a description of response types).

| Sender | Triggered by | Triggers |
| --- | --- | --- |
| `FMS`  | Escort creation or updates | The AV to start accepting and adhere to the escorts, and fire off `ActivateEscortResponseV1` messages |

## Message Attributes

The `ActivateEscortRequestV1` message consists of the following properties.

| Key | Value | Format | Required | Description |
| --- | :---: | :---: | :---: | --- |
| `"EscorterId"` | UUID | UUID | False | Identifier of the Escorter Vehicle |
| `"EscortId"` | UUID | UUID | True | A unique identifier for the escort operation |
| `"Length"` | m | double | True | Length of protection Zone |
| `"Width"` | m | double | True | Width of protection Zone, applicable when Escort Convoy is in open area. |
| `"EscortPosition"` | EscortPositionV1 | EscortPositionV1 | True | Current position of the escort |

>[!IMPORTANT]
> The AV will reject the request if the escort position is in some way faulty



## Examples
### Typical Message
```JSON
{
  "Protocol":"Open-Autonomy",
  "Version": 1,
  "Timestamp": "2018-10-31T09:30:10.435Z",
  "EquipmentIds": [
    "f0c3d5ab-2d6e-4a12-b9d9-9eaf1efc0abc",
    "9b8b6d54-1234-4c81-a911-5555bbbb7777"
  ],
  "ActivateEscortRequestV1": {
    "EscorterId": "11111111-2222-3333-4444-555555555555",
    "EscortId": "00000000-0000-0000-0000-000000000001",
    "Length": 200.0,
    "Width": 6.0
    "EscortPosition": {
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
      "SpeedAccuracy": 0.2,
    }
  }
}

```
