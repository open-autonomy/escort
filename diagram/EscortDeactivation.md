# Escort Deactivation
When an escort is deactivated, the FMS sends a request to the AHS to deactivate the escort on all AVs that are currently adhering to it. The AHS then communicates with each AV to remove the active escort zone.

> [!IMPORTANT]
> - All systems shall implement idempotency when managing Escort Deactivations.
> - To avoid unmanageable synchronization failures, AVs should accept Escort Deactivations for escorts that are unknown or already removed in AV memory (treat as success / NoOp).

Assuming the escort already exists in the FMS

```mermaid
sequenceDiagram
    actor Escorter
    participant FMS
    participant AHS
    participant AV 1
    participant AV N

    Note over FMS,AV N: Deactivation Sequence
    Escorter->>FMS: Deactivate Escort (UI Action)
    FMS-->>FMS: Escort Pending Deactivation
    FMS-->>Escorter: Pending Deactivation

    par AV 1
        FMS->>AHS: DeactivateEscortRequestV1
        AHS->>AV 1: DeactivateEscortCommand
        AV 1->>AV 1: Remove zone
        AV 1->>AHS: DeactivationStatus
        AHS->>FMS: DeactivateEscortResponseV1
    and AV N
        FMS->>AHS: DeactivateEscortRequestV1
        AHS->>AV N: DeactivateEscortCommand
        AV N->>AV N: Remove zone
        AV N->>AHS: DeactivationStatus
        AHS->>FMS: DeactivateEscortResponseV1
    end

    Note over FMS: All AVs deactivated escort
    FMS-->>FMS: Escort Deactivated
    FMS-->>Escorter: Escort Deactivated
```

## Out-of-Sync Deactivation Handling

```mermaid
sequenceDiagram
    participant FMS
    participant AHS
    participant AV

    FMS->>AHS: DeactivateEscortRequestV1
    alt AHS/AV missing escort
        AHS-->>FMS: DeactivateEscortResponseV1(status=NoOp)
    else Present
        AHS->>AV: DeactivateEscortCommand
        AV->>AV: Remove zone
        AV->>AHS: DeactivationStatus
        AHS-->>FMS: DeactivateEscortResponseV1(status=Success)
    end
```
