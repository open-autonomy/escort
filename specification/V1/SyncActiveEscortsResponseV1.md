# SyncActiveEscortsResponseV1

This message is sent by the Autonomous Haulage System (AHS) in response to the `SyncActiveEscortsRequestV1` messages from Fleet Management System  (FMS). The Autonomous Vehicle (AV) **MUST** internally activate all the escorts provided in the `SyncActiveEscortsRequestV1` message in order to operate. If any escort cannot be activated, the AV should reject the request using the `SyncActiveEscortsResponseV1` message and **MUST** not operate.

| Sender | Triggered by | Triggers |
| --- | --- | --- |
| `AHS`  | `SyncActiveEscortsRequestV1` |  |


## Message Attributes

The `SyncActiveEscortsResponseV1` message consists of the following properties.

| Key | Value | Format | Required | Description |
| --- | :---: | :---: | :---: | --- |
| `"ResponseId"` | ResponseId | UUID | True | A unique identifier for the response. This is used to match the response message with the `SyncActiveEscortsRequestV1` message. |
| `"Status"` | [`Activated`, `Rejected`] | String | True | Indicates whether the AV has successfully activated received the policy escorts. <br/> - `Activated`: The AV has activated all escorts and is adhering to their associated policies. <br/> - `Rejected`: The AV cannot adhere to one or more of the policies. In this case, the AV must not operate as it cannot guarantee safety. |
| `"ActivatedEscorts"` | Array[`EscortIdObject`] | Array[] | True | A list of `EscortIdObject` that indicates the escorts that have been activated by the AV |

>[!NOTE]
> The top-level message headers should contain the `EquipmentId` which indicate the origin AV of the `SyncActiveEscortsResponseV1` message

### EscortIdObject
| Key | Value | Format | Required | Description |
| --- | :---: | :---: | :---: | --- |
| `"EscortId"` | EscortId | UUID | True | The policy escort id |


## Examples
### Typical Message
```JSON
{
  "Protocol": "Open-Autonomy",
  "Version": 1,
  "Timestamp": "2021-09-01T12:00:00Z",
  "EquipmentId": "e4de3723-a315-4506-b4e9-537088a0eabf",
  "SyncActiveEscortsResponseV1": {
    "ResponseId": "00000000-0000-0000-0000-000000000001",
    "Status": "Activated",
    "ActivatedEscorts": [
      {
        "EscortId": "00000000-0000-0000-0000-000000000001"
      },
      {
        "EscortId": "00000000-0000-0000-0000-000000000002"
      },
      {
        "EscortId": "00000000-0000-0000-0000-000000000003"
      }
    ]
  }
}
```