# Message Header

This section defines the common top‑level envelope used by all messages in the Escort Protocol. Every application payload (e.g., [ActivateEscortRequestV1](ActivateEscortRequestV1.md), [ActivateEscortResponseV1](ActivateEscortResponseV1.md)) SHALL be embedded as a single additional top‑level property alongside the header attributes defined here.

## Structure
The header conveys protocol identification, versioning, temporal context, and equipment addressing.

## Message Attributes
| Key | Value | Format | Required | Description |
| --- |:---:|:---:|:---:| --- |
| `"Protocol"` | `"Open-Autonomy"` | string | True | The protocol of the messages. |
| `"Version"` | 1 | integer | True | The version of the protocol. |
| `"Timestamp"` | DateTime | ISO 8601 (UTC)  | True | The date-time of when the message is sent in ISO 8601 format. |
| `"EquipmentId"` | EquipmentId | UUID | True | Target or source equipment identifier. UUID as defined in the ISO 23725 - FleetDefinitionV2. |

## Examples
Example escort activation request (FMS -> AV):
```json
{
  "Protocol":"Open-Autonomy",
  "Version": 1,
  "Timestamp": "2025-10-20T09:30:10.435Z",
  "EquipmentId": "f0c3d5ab-2d6e-4a12-b9d9-9eaf1efc0abc",
  "ActivateEscortRequestV1": {...}
}
```

Example escort activation response (AV -> FMS):
```json
{
  "Protocol":"Open-Autonomy",
  "Version": 1,
  "Timestamp": "2025-10-20T09:30:11.012Z",
  "EquipmentId": "f0c3d5ab-2d6e-4a12-b9d9-9eaf1efc0abc",
  "ActivateEscortResponseV1": {...}
}
```
