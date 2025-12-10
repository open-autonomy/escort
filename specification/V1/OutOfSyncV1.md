# OutOfSyncV1

This message is sent by the Autonomous Haulage System (AHS) to Fleet Management System (FMS) to notify that the Autonomous Vehicle (AV) cannot guarantee that it has an up-to-date list of active escorts (e.g., the AV was previously offline), and requires the FMS to send the current set of active escorts through a [SyncActiveEscortsRequestV1](SyncActiveEscortsRequestV1.md). 

> [!IMPORTANT]
> AVs that are out of sync with the active escorts must not operate until they have received and internally activated these escorts. There is no "Pending" state for the activation of out of sync escorts.

| Sender | Triggered by | Triggers |
| --- | --- | --- |
| `AHS`  | AV re-connecting to AHS with outdated escorts | FMS to send [SyncActiveEscortsRequestV1](SyncActiveEscortsRequestV1.md) |

## Message Attributes

The `OutOfSyncV1` message consists of the following property:

| Key | Value | Format | Required | Description |
| --- | :---: | :---: | :---: | --- |
| `"EventId"` | EventId | UUID | Shall | Correlation identifier for this out-of-sync notification. The FMS SHALL send exactly one [SyncActiveEscortsRequestV1](/specification/V1/SyncActiveEscortsRequestV1.md) per distinct `EventId` and SHALL reuse this value as `SyncActiveEscortsRequestV1.RequestId`. <br>If multiple `OutOfSyncV1` messages with the same `EventId` are received (duplicates/retries), the FMS MUST NOT issue additional sync requests and MAY ignore them after confirming the initial request was sent. |

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
    "OutOfSyncV1": {
        "EventId": "00000000-0000-0000-0000-000000000001"
    }
}
```
