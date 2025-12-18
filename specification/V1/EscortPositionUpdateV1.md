# EscortPositionUpdateV1

This message is sent by the Fleet Management System (FMS) to the Autonomous Haulage System (AHS) at a nominal frequency of 1 Hz to report current position and motion state of the active Escorter. It enables Autonomous Vehicles (AVs) to maintain and update their Avoidance Zone relative to the Protection Zone.

| Sender | Triggered by | Triggers |
| --- | --- | --- |
| FMS | Escort Pending or Active (after [ActivateEscortRequestV1](ActivateEscortRequestV1.md)) and periodic (every ~1 s) | Provides latest Escorter pose for AV prediction & Avoidance Zone constraint |

## Message Attributes

The `EscortPositionUpdateV1` payload object contains the following properties (top-level message header fields such as `Protocol`, `Version`, `Timestamp` and `EquipmentId` are defined in `MessageHeader.md`).

| Key | Req. Level | Type | Unit / Format | Description |
| --- | --- | --- | --- | --- |
| `"EscortId"` | Shall | string | UUID | Identifier of the Escort instance. |
| `"Timestamp"` | Shall | string | ISO 8601 | Timestamp when the position sample was measured (this is different to the Timestamp in the message header, which is the of the message). See note below.|
| `"StationId"` | should  | string | Id | Unique ID on redundant network channel e.g. V2X. Content may vary depending on the technology in use. |
| `"Pose.Latitude"` | Shall | double | degrees (WGS84) | 6+ decimal places (≈0.11 m). |
| `"Pose.Longitude"` | Shall | double | degrees (WGS84) | 6+ decimal places. |
| `"Pose.Elevation"` | Shall | double | meters | Elevation relative to WGS84 ellipsoid (2 decimals). |
| `"Pose.Heading"` | Shall | double | degrees | 0 – 359.999...; 0 = true north, clockwise increase. A value of 360° is invalid and should be sent as 0°. |
| `"Speed"` | Shall | double | m/s | Instantaneous ground speed. |
| `"Accuracy.Latitude"` | Should | double | meters (1σ) | 1‑sigma horizontal positional accuracy latitude component. |
| `"Accuracy.Longitude"` | Should | double | meters (1σ) | 1‑sigma horizontal positional accuracy longitude component. |
| `"Accuracy.Elevation"` | Should | double | meters (1σ) | 1‑sigma vertical accuracy. |
| `"Accuracy.Heading"` | Should | double | degrees (1σ) | 1‑sigma heading accuracy. |
| `"Accuracy.Speed"` | Should | double | m/s (1σ) | 1‑sigma speed accuracy. |

> [!NOTE]
> The Timestamp of the position measurement shall be monotonically increasing and sourced from a NTP Stratum 1 time source or equivalent. Leap seconds should not be smeared, and instead represented as suggested in ISO 8601, i.e. positive leap seconds are to be represented as [23:59:60Z] and negative leap seconds by the ommission of [23:59:59Z].

> [!NOTE]
> - Accuracy fields help AVs tune dynamic buffer growth.

> [!NOTE]
> Why StationId?
> - Technologies implementing vehicle-to-everytyhing (V2X) capabilites or other peer-to-peer (p2p) messaging mechanisms can typically operate with lower latency than the `EscortPositionUpdateV1` messages that are relayed via the controlling FMS solution, therefore StationId of the Escorter is valuable information to the AV.
>
> Why StationId in Position Update Message?
> - V2X StationIds are liable to change, therefore this is part of the update rather than the activation message.

## Example
```json
{
  "Protocol": "Open-Autonomy",
  "Version": 1,
  "Timestamp": "2025-10-20T10:15:30.125Z",
  "EquipmentId": "f0c3d5ab-2d6e-4a12-b9d9-9eaf1efc0abc",
  "EscortPositionUpdateV1": {
    "EscortId": "11111111-2222-3333-4444-555555555555",
    "Timestamp": "2025-10-20T10:15:29.987Z",
    "StationId": "23983958",
    "Speed": 0.2,
    "Pose": {
      "Latitude": 59.1546127,
      "Longitude": 17.6212361,
      "Elevation": 428.32,
      "Heading": 87.8,
    },
    "Accuracy": {
      "Latitude": 0.8,
      "Longitude": 0.9,
      "Elevation": 1.5,
      "Heading": 2.0,
      "Speed": 0.2
    }
  }
}
```

## Validation Rules
- Shall be published at 1 Hz (±100 ms tolerance recommended).
- Shall monotonically increase `Timestamp` (no duplicates or regressions within a session).
- If accuracy metrics are unknown, omit the respective fields rather than sending zero.

## Failure / Degradation Handling
// TODO: Document how the system should respond to message loss, unavailable data, etc..
| Condition | Recommended Behavior |
| --- | --- |

## Versioning
Additional optional fields MAY be appended in future minor protocol versions; consumers SHALL ignore unknown fields.
