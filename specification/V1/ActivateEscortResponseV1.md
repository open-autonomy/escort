# ActivateEscortResponseV1

This message is sent by the Autonomous Haulage System (AHS) in response to the `ActivateEscortRequestV1` messages from the Fleet Management System (FMS), indicating whether the Autonomous Vehicle (AV) has accepted, activated or rejected the escort request.

| Sender | Triggered by | Triggers |
| --- | --- | --- |
| `AHS`  | `ActivateEscortRequestV1` |  |


## Message Attributes

The `ActivateEscortResponseV1` message consists of the following properties.

| Key | Value | Format | Required | Description |
| --- | :---: | :---: | :---: | --- |
| `"EscortId"` | EscortId | UUID | True | The escort id in which the truck is responding to |
| `"Status"` | [`Accepted`, `Activated`, `Rejected`] | String | True | Determine whether the AV have accepted, activated or rejected the escort request.<br/>- `Accepted`: The AV has processed the escort and has scheduled it for activation.<br/>- `Activated`: The AV has activated the escort as it is now adhering to the associated policies.<br/>- `Rejected`: The AV is unable to activate the escort due to an error explained in `"Reason"` |
| `"Reason"` | String Enum | String | False | The reason for rejecting escort request |

> [!IMPORTANT]

- AV shall only `Reject` a escort request if there is an error preventing it from processing the escort request.
- An AV may choose to send an `Activate` response without sending an `Accepted` response first. In this case, the AV is indicating that it has activated the escort and is adhering to the specified policies. An AV shall send an `Accepted` response if it cannot immediately adhere to the escort.

> AV shall only `Reject` a escort request if there is an error preventing it from processing the escort request.

> [!NOTE]
> The top-level message headers should contain the `EquipmentId` which indicate the origin AV of the `ActivateEscortResponseV1` message


## Examples
### Typical Message
```JSON
{
  "Protocol": "Open-Autonomy",
  "Version": 1,
  "Timestamp": "2021-09-01T12:00:00Z",
  "EscorterId": "11111111-2222-3333-4444-555555555555",
  "EquipmentId": "e4de3723-a315-4506-b4e9-537088a0eabf",
  "ActivateEscortResponseV1": {
    "EscortId": "123e4567-e89b-12d3-a456-426614174000",
    "Status": "Activated"
  }
}
```

### Rejection Message
```JSON
{
  "Protocol": "Open-Autonomy",
  "Version": 1,
  "Timestamp": "2021-09-01T12:00:00Z",
  "EscorterId": "11111111-2222-3333-4444-555555555555",
  "EquipmentId": "e4de3723-a315-4506-b4e9-537088a0eabf",
  "ActivateEscortResponseV1": {
    "EscortId": "123e4567-e89b-12d3-a456-426614174000",
    "Status": "Rejected",
    "Reason": "UnexpectedOffline"
  }
}
```