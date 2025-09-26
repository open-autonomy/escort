# DeactivateEscortResponseV1

This message is sent by the Autonomous Haulage System (AHS) in response to the `DeactivateEscortRequestV1` message from the Fleet Management System (FMS), indicating whether the Autonomous Vehicle (AV) was successful in removing the escort from its internal set of active escorts.

| Sender | Triggered by | Triggers |
| --- | --- | --- |
| `AHS`  | `DeactivateEscortRequestV1` |  |

## Message Attributes

The `DeactivateEscortResponseV1` message consists of the following properties.

| Key | Value | Format | Required | Description |
| --- | :---: | :---: | :---: | --- |
| `"EscortId"` | EscortId | UUID | True | The escort id in which the truck is responding to |
| `"Status"` | [`Accepted`] | String | True |  |

>[!NOTE]
> The top-level message headers should contain the `EquipmentId` which indicate the origin AV of the `DeactivateEscortResponseV1` message

## Examples
### Typical Message
```JSON
{
  "Protocol": "Open-Autonomy",
  "Version": 1,
  "Timestamp": "2021-09-01T12:00:00Z",
  "EscorterId": "11111111-2222-3333-4444-555555555555",
  "EquipmentId": "e4de3723-a315-4506-b4e9-537088a0eabf",
  "DeactivateEscortResponseV1": {
    "EscortId": "123e4567-e89b-12d3-a456-426614174000",
    "Status": "Accepted"
  }
}
```