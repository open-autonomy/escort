# OutOfSyncV1

This message is sent by the Autonomous Haulage System (AHS) to Fleet Management System (FMS) to notify that the Autonomous Vehicle (AV) cannot guarantee that it has an up-to-date list of active escorts (e.g., the AV was previously offline), and requires the FMS to send the current set of active escorts through a `SyncActiveEscortsRequestV1`. 

> [!IMPORTANT]
> AV that are out of sync with the active escorts must not operate until they have received and internally activated these escorts.

| Sender | Triggered by | Triggers |
| --- | --- | --- |
| `AHS`  | AV re-connecting to AHS with outdated escorts | FMS to send `SyncActiveEscortsRequestV1` |

## Message Attributes

The `OutOfSyncV1` message does not contain any additional attributes beyond the standard message headers.

>[!NOTE]
> The top-level message headers should contain the `EquipmentId` which indicate the origin AV of the `OutOfSyncV1` message 

## Examples
### Typical Message
```json
{
    "Protocol": "Open-Autonomy",
    "Version": 1,
    "Timestamp": "2024-08-23T08:19:55.621Z",
    "EquipmentId": "f0c3d5ab-2d6e-4a12-b9d9-9eaf1efc0abc",
    "OutOfSyncV1": {}
}
```
