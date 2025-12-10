# SyncActiveEscortsResponseV1

This message is sent by the Autonomous Haulage System (AHS) in response to the [SyncActiveEscortsRequestV1](SyncActiveEscortsRequestV1) messages from Fleet Management System  (FMS). The Autonomous Vehicle (AV) **MUST** internally activate all the escorts provided in the [SyncActiveEscortsRequestV1](SyncActiveEscortsRequestV1) message in order to operate. If any escort cannot be activated, the AV should reject the request using the `SyncActiveEscortsResponseV1` message and **MUST** not operate.
 There is no "Pending" state for the activation of out of sync escorts.

| Sender | Triggered by | Triggers |
| --- | --- | --- |
| `AHS`  | [SyncActiveEscortsRequestV1](SyncActiveEscortsRequestV1) | The AV to start accepting and adhering to the escort. |


## Message Attributes

The `SyncActiveEscortsResponseV1` message consists of the following properties.

| Key | Value | Format | Required | Description |
| --- | :---: | :---: | :---: | --- |
| `"ResponseId"` | UUID | String | Shall | A unique identifier for the response. This is used to match the response message with the [SyncActiveEscortsRequestV1](SyncActiveEscortsRequestV1) message. |
| `"Status"` | [`Activate`, `Rejected`] | String | Shall | Indicates whether the AV has successfully activated the escorts. <br/> - `Activate`: The AV has activated all escorts and is adhering to their associated protection zones. <br/> - `Rejected`: The AV cannot adhere to one or more of the escorts. In this case, the AV must not operate as it cannot guarantee safety. |
| `"Reason"` | String Enum | String | May | High-level reason for rejecting the entire set when Status = Rejected (e.g. a global failure). Use per-escort reasons in RejectedEscorts for granular causes. |
| `"RejectedEscorts"` |  | Array[[RejectedEscortObject](#RejectedEscortObject)] | May | Granular list of escorts the AV failed to activate along with per-escort rejection reasons. Present only when `Status` = `Rejected`. |

> [!NOTE]
> The top-level message header SHALL contain `EquipmentId` identifying the origin AV of this `SyncActiveEscortsResponseV1` message.

### RejectedEscortObject
| Key | Value | Format | Required | Description |
| --- | :---: | :---: | :---: | --- |
| `"EscortId"` | UUID | String | Shall | Identifier of the escort that failed activation. |
| `"Reason"` | String Enum | String | Shall | Specific reason this escort could not be activated (e.g. `MaxActiveEscortsExceeded`, `...`, `...`). |

> [!TIP]
> Use `Reason` at the top level for a single global failure (e.g. `MaxActiveEscortsExceeded`). Use `RejectedEscorts[i].Reason` for individual zones when some, but not all, failed. If both are present, the top-level `Reason` should summarize while per-zone reasons provide detail.

See [ActivateEscortResponseV1 - Rejection Reasons](ActivateEscortResponseV1.md#rejection-reasons) for recommended rejection reason enumerations.

### Examples
#### Activated (All Escorts Succeeded)
```json
{
  "Protocol": "Open-Autonomy",
  "Version": 1,
  "Timestamp": "2021-09-01T12:00:00Z",
  "EquipmentId": "e4de3723-a315-4506-b4e9-537088a0eabf",
  "SyncActiveEscortsResponseV1": {
    "ResponseId": "00000000-0000-0000-0000-000000000001",
    "Status": "Activated"
  }
}
```

#### Rejected (Global Reason Only)
```json
{
  "Protocol": "Open-Autonomy",
  "Version": 1,
  "Timestamp": "2021-09-01T12:00:00Z",
  "EquipmentId": "e4de3723-a315-4506-b4e9-537088a0eabf",
  "SyncActiveEscortsResponseV1": {
    "ResponseId": "00000000-0000-0000-0000-000000000001",
    "Status": "Rejected",
    "Reason": "MaxActiveEscortsExceeded"
  }
}
```

#### Rejected (Per-Escort Granular Reasons)
```json
{
  "Protocol": "Open-Autonomy",
  "Version": 1,
  "Timestamp": "2021-09-01T12:00:00Z",
  "EquipmentId": "e4de3723-a315-4506-b4e9-537088a0eabf",
  "SyncActiveEscortsResponseV1": {
    "ResponseId": "00000000-0000-0000-0000-000000000001",
    "Status": "Rejected",
    "RejectedEscorts": [
      { "EscortId": "00000000-0000-0000-0000-000000000001", "Reason": "..." },
      { "EscortId": "00000000-0000-0000-0000-000000000002", "Reason": "..." }
    ]
  }
}
```