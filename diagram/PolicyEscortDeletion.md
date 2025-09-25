# Escort Deletion
When a escort is deleted, the Fleet Management System (FMS) will send a request to the Autonomous Haulage System (AHS) to deactivate the escort on all Autonomous Vehicles (AV) that are currently adhering to it. The AHS will then communicate with each AV to deactivate the escort.

> [!IMPORTANT]
> - All systems shall implement idempotency when managing Escort Deletions.
> - To avoid unmanagable synchronization failures, AVs should accept Escort Deletions for escorts that do not exist in the AVs memory.

Assuming the escort already exists in the FMS

```mermaid
sequenceDiagram
    participant User
    participant FMS
    participant AHS
    participant AV 1
    participant AV N

    Note over FMS,AV 1: On Connect Sequence
    User->>FMS: Delete Escort
    FMS-->>FMS: Escort Pending Delete
    FMS-->+User: Pending Delete

    par AV 1
        FMS->>AHS: Sends DeactivateEscortRequestV1 to AV 1
        AHS->>AV 1: Delete Escort
        AV 1->>AHS: Accept
        AHS->>FMS: DeactivateEscortResponseV1
    and AV N
        FMS->>AHS: Sends DeactivateEscortRequestV1 to AV N
        AHS->>AV N: Delete Escort
        AV N->>AHS: Accept
        AHS->>FMS: DeactivateEscortResponseV1
    end

    User-->-FMS: Pending Delete

    Note Over FMS: All AVs deactivated Escort
    FMS-->>FMS: Escort Deleted
    FMS-->>User: Escort Deleted
```