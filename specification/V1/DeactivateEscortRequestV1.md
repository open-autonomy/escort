# DeactivateEscortRequestV1

This message is sent by the Fleet Management System (FMS) to Autonomous Haulage System (AHS) to indicate a escort has been flagged for deletion, and each Autonomous Vehicle (AV) should remove it from its own internal set of active escorts.

| Sender | Triggered by | Triggers |
| --- | --- | --- |
| `FMS`  | Deletion of escort | AV to internally deactivate the escort, and response with `DeactivateEscortResponseV1` message |

## Message Attributes

The `DeactivateEscortRequestV1` message consists of the following properties.

| Key | Value | Format | Required | Description |
| --- | :---: | :---: | :---: | --- |
| `"EscortId"` | EscortId | UUID | True | The escort id to deactivate |

>[!NOTE]
> The top-level message header SHALL contain `EquipmentId` identifying the single AV this request addresses.


## Examples
### Typical Message
```json
{
  "Protocol": "Open-Autonomy",
  "Version": 1,
  "Timestamp": "2021-09-01T12:00:00Z",
  "EquipmentId": "f0c3d5ab-2d6e-4a12-b9d9-9eaf1efc0abc",
  "DeactivateEscortRequestV1": {
    "EscortId": "123e4567-e89b-12d3-a456-426614174000"
  }
}
```
