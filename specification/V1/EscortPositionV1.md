# EscortPositionV1

This message is sent by the Fleet Management System (FMS) to the Autonomous Haulage System (AHS) at a frequency of 1 Hz to report the current position and motion state of the active escorting vehicle (Escorter). The purpose is to enable all Autonomous Vehicles (AVs) to maintain an up‑to‑date dynamic protection zone around the escort.

| Sender | Triggered by | Triggers |
| --- | --- | --- |
| `FMS` | Escort activation (immediately after sending `ActivateEscortRequestV1`) and every 1 s while active | AHS to forward/update internal AV state used for protection zone maintenance |

> [!IMPORTANT]
> FMS SHALL begin sending `EscortPositionV1` messages immediately after transmitting an `ActivateEscortRequestV1` (shall NOT wait for any `ActivateEscortResponseV1`).
>
> If more than 2 consecutive `EscortPositionV1` messages are missed (configurable), AVs MAY expand protection margins or fail-safe according to site request.

> [!IMPORTANT]
> `EquipmentIds` is an array (rather than the single `EquipmentId` used in the standard header) because this message is broadcast at 1 Hz to all active AVs. By listing all intended recipients in one payload the FMS avoids transmitting an identical position update separately to each AV, reducing bandwidth and processing overhead. This is a deliberate, message-specific deviation from the standard header pattern to support high‑frequency distribution efficiency.

## Message Attributes

The `EscortPositionV1` payload object contains the following properties (top-level message header fields such as `Protocol`, `Version`, `Timestamp`, `EquipmentId`, and `EscorterId` are defined in `MessageHeader.md`).

| Key | Req. Level | Type | Unit / Format | Description |
| --- | --- | --- | --- | --- |
| `"EquipmentIds"` | shall | Array[UUID] | UUID list | List of EquipmentIds (AV identifiers) this position is intended for; MAY be the full active set, MAY be a targeted subset. |
| `"Timestamp"` | shall | DateTime | ISO 8601 (GPS time if available) | Timestamp when the position sample was measured (NOT when message transmitted). |
| `"Latitude"` | shall | decimal | degrees (WGS84) | 6+ decimal places (≈0.11 m). |
| `"Longitude"` | shall | decimal | degrees (WGS84) | 6+ decimal places. |
| `"Elevation"` | shall | decimal | meters | Elevation relative to WGS84 ellipsoid (2 decimals). |
| `"Heading"` | shall | integer | degrees | 0–359; 0 = true north, clockwise increase. |
| `"Speed"` | shall | decimal | m/s | Instantaneous ground speed (consider conversion from native km/h). |
| `"LatitudeAccuracy"` | should | decimal | meters (1σ) | 1‑sigma horizontal positional accuracy latitude component. |
| `"LongitudeAccuracy"` | should | decimal | meters (1σ) | 1‑sigma horizontal positional accuracy longitude component. |
| `"ElevationAccuracy"` | should | decimal | meters (1σ) | 1‑sigma vertical accuracy. |
| `"HeadingAccuracy"` | could | decimal | degrees (1σ) | 1‑sigma heading accuracy. |
| `"SpeedAccuracy"` | could | decimal | m/s (1σ) | 1‑sigma speed accuracy (optional). |
| `"VelocityVector"` | could | Object | m/s | Optional decomposed velocity components. |
| `"VelocityVector.vx"` | could | decimal | m/s | X component in local ENU (East). |
| `"VelocityVector.vy"` | could | decimal | m/s | Y component in local ENU (North). |
| `"VelocityVector.vz"` | could | decimal | m/s | Z component (Up). |

> [!NOTE]
> - At minimum: `EscorterId`, `EquipmentIds`, `Timestamp`, `Latitude`, `Longitude`, `Elevation`, `Heading`, `Speed` MUST be present.
> - Accuracy fields help AVs tune dynamic buffer growth; include when available.
> - If GPS time differs from system send time by > 250 ms, implementers SHOULD still populate `Timestamp` with measurement time (not transmission time).
>
> The `EquipmentIds` array allows targeted updates (e.g., only AVs within a geographic radius). FMS SHOULD include all active AVs unless bandwidth optimization is necessary.

## Example
```JSON
{
  "Protocol": "Open-Autonomy",
  "Version": 1,
  "Timestamp": "2025-09-26T10:15:30.125Z",
  "EscorterId": "11111111-2222-3333-4444-555555555555",
  "EquipmentIds": [
    "f0c3d5ab-2d6e-4a12-b9d9-9eaf1efc0abc",
    "9b8b6d54-1234-4c81-a911-5555bbbb7777"
  ],
  "EscortPositionV1": {
    "Timestamp": "2025-09-26T10:15:29.900Z",
    "Latitude": 59.1546127,
    "Longitude": 17.6212361,
    "Elevation": 428.32,
    "Heading": 87,
    "Speed": 4.2,
    "LatitudeAccuracy": 0.8,
    "LongitudeAccuracy": 0.9,
    "ElevationAccuracy": 1.5,
    "HeadingAccuracy": 2.0,
    "SpeedAccuracy": 0.2,
    "VelocityVector": {
      "vx": 4.15,
      "vy": 0.35,
      "vz": 0.00
    }
  }
}
```

## Validation Rules
- SHALL be published at 1 Hz (±100 ms tolerance recommended).
- SHALL monotonically increase `Timestamp` (no duplicates or regressions within a session).
- SHOULD omit `VelocityVector` if any component is NaN or unavailable (omit entire object).
- SHALL include `Speed` even if zero.
- If accuracy metrics are unknown, omit the respective fields rather than sending zero (unless zero is a valid measured accuracy).

## Failure / Degradation Handling
| Condition | Recommended Behavior |
| --- | --- |
| Missing consecutive > 2 samples | AV enlarges protective zone using last known speed & heading decay. |
| Heading unavailable | Set `Heading` to last valid; omit `HeadingAccuracy`. |
| Speed unavailable | Use 0 and flag internally (do not omit field). |
| Position accuracy > site threshold | AV may treat escort as stationary hazard until quality recovers. |

## Versioning
Additional optional fields MAY be appended in future minor protocol versions; consumers SHALL ignore unknown fields.
