# ActivateEscortResponseV1

This message is sent by the Autonomous Haulage System (AHS) in response to an `ActivateEscortRequestV1` from the Fleet Management System (FMS). It reports the current escort activation state for a specific Autonomous Vehicle (AV).

| Sender | Triggered by | Triggers |
| --- | --- | --- |
| AHS | Receipt of valid `ActivateEscortRequestV1` | |


## Message Attributes

The `ActivateEscortResponseV1` message consists of the following properties.

| Key | Value | Format | Required | Description |
| --- | :---: | :---: | :---: | --- |
| `"EscortId"` | EscortId | UUID | True | Identifier of the escort instance being acknowledged. MUST match the request. |
| `"Status"` | [`Pending`, `Active`, `Rejected`] | String | True | Activation state for this AV. |
| `"Reason"` | String Enum | String | False | Present only when `Status` = `Rejected`; provides machine‑readable cause. |

### Status Semantics
| Status | Meaning |
| --- | --- |
| `Pending` | AV has accepted escort parameters and is transitioning to `Active` (e.g., internal planning adjustments in progress). |
| `Active` | AV has applied Protection / Avoidance Zone constraints and is enforcing the escort. |
| `Rejected` | AV is unable to activate the escort; a `Reason` SHOULD be supplied. |

> [!IMPORTANT]
> - An AV SHALL send `Rejected` only if an error prevents activation.
> - An AV MAY send `Active` directly (skipping `Pending`) if activation is immediate.
> - If activation cannot be immediate, AV SHALL first send `Pending`.

## Rejection Reasons
Recommended enumeration (implementations MAY extend):
- `UnexpectedOffline` – AV not reachable.
- `TooManyActiveEscorts` – AV can not safely manage another escort.
- `InvalidPosition` – Position update contains invalid data.
- `InvalidProtectionZone` – Protection zone parameters wrongly configured.

## Example – Active Response
```json
{
  "Protocol": "Open-Autonomy",
  "Version": 1,
  "Timestamp": "2025-10-20T12:00:00Z",
  "EquipmentId": "f0c3d5ab-2d6e-4a12-b9d9-9eaf1efc0abc",
  "ActivateEscortResponseV1": {
    "EscortId": "123e4567-e89b-12d3-a456-426614174000",
    "Status": "Active"
  }
}
```

## Example – Rejected Response
```json
{
  "Protocol": "Open-Autonomy",
  "Version": 1,
  "Timestamp": "2025-10-20T12:00:05Z",
  "EquipmentId": "f0c3d5ab-2d6e-4a12-b9d9-9eaf1efc0abc",
  "ActivateEscortResponseV1": {
    "EscortId": "123e4567-e89b-12d3-a456-426614174000",
    "Status": "Rejected",
    "Reason": "UnexpectedOffline"
  }
}
```
