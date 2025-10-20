# Message Header

This section defines the common top‑level envelope used by all messages in the Escort Protocol. Every application payload (e.g., `ActivateEscortRequestV1`, `ActivateEscortResponseV1`) SHALL be embedded as a single additional top‑level property alongside the header attributes defined here.

## Structure
The header conveys protocol identification, versioning, temporal context, and equipment addressing.

## Message Attributes
| Key | Value | Format | Required | Description |
| --- |:---:|:---:|:---:| --- |
| `"Protocol"` | `"Open-Autonomy"` | string | True | The protocol of the messages. |
| `"Version"` | 1 | integer | True | The version of the protocol. |
| `"Timestamp"` | DateTime | ISO 8601 (UTC)  | True | The date-time of when the message is sent in ISO 8601 format. |
| `"EquipmentIds"` | Array<EquipmentId> | UUID | True | Target or source equipment identifiers. UUID as defined in the ISO 23725 - FleetDefinitionV2. FMS→AHS broadcast messages MAY list multiple recipients. AHS→FMS messages SHALL list exactly one UUID (the sender). Array MUST be non‑empty and contain unique UUIDs. |

> [!IMPORTANT]
> `EquipmentIds` is an array (rather than the single `EquipmentId`) because some messages are broadcasted at 1 Hz to all active AVs. By listing all intended recipients in one payload the FMS avoids transmitting an identical position update separately to each AV, reducing bandwidth and processing overhead. This is a deliberate, deviation from the standard header pattern to support high‑frequency distribution efficiency.


##Examples
Example escort activation request broadcast (multiple recipients):
```json
{
  "Protocol":"Open-Autonomy",
  "Version": 1,
  "Timestamp": "2025-10-20T09:30:10.435Z",
  "EquipmentIds": [
    "f0c3d5ab-2d6e-4a12-b9d9-9eaf1efc0abc",
    "9b8b6d54-1234-4c81-a911-5555bbbb7777"
  ],
  "CorrelationId": "22222222-3333-4444-5555-666666666666",
  "MessageId": "aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee",
  "ActivateEscortRequestV1": { "EscortId": "00000000-0000-0000-0000-000000000001" }
}
```

Example escort activation response (single sender):
```json
{
  "Protocol":"Open-Autonomy",
  "Version": 1,
  "Timestamp": "2025-10-20T09:30:11.012Z",
  "EquipmentIds": [
    "f0c3d5ab-2d6e-4a12-b9d9-9eaf1efc0abc"
  ],
  "CorrelationId": "22222222-3333-4444-5555-666666666666",
  "MessageId": "bbbbbbbb-cccc-dddd-eeee-ffffffffffff",
  "ActivateEscortResponseV1": { "EscortId": "00000000-0000-0000-0000-000000000001", "Status": "Active" }
}
```
