# Escort Activation
When an escort is created, the FMS initiates the activation process by sending [ActivateEscortRequestV1`](/specification/V1/ActivateEscortRequestV1.md) messages to the AHS for each of the AV defined in the  [Fleet Definition](/specification/V1/FleetDefinitionV2.md). The AHS then communicates with each of the AVs to activate the escort internally. This document includes the full life cycle: activation attempts, steady‑state position pulsing, and deactivation.

> [!IMPORTANT]
> All systems shall implement idempotency when managing Escort Activations.

## Escort Overview Flow

```mermaid
sequenceDiagram
    actor Escorter
    participant FMS
    participant AHS
    participant AV

    Escorter->>FMS: Activate Escort (UI Action)
    activate FMS
    activate AHS
    activate AV
    FMS->>AHS: ActivateEscortRequestV1
    AHS->>AV: Activate Escort
    AV->>AV: Escort adherence pending
    AV-->>AHS: Escort Pending
    AHS-->>FMS: ActivateEscortResponseV1(status=Pending)

    AV->>AV: Adhering to Escort
    AV-->>AHS: Escort Activated
    AHS-->>FMS: ActivateEscortResponseV1(status=Activated)
    FMS-->>FMS: All AVs Activated
    FMS-->>Escorter: Escort activated
    Note over FMS: DO NOT wait for response<br/>to start sending EscortPositionUpdateV1
    loop Escort loop (1 Hz)
        Escorter-->>FMS: (Position Source)
        FMS-->>AHS: EscortPositionUpdateV1
        AHS->>AV: PositionUpdate
        AV->>AV: Update Avoidance Zone geometry
    end
    %% Deactivation flow moved to EscortDeactivation.md
    deactivate AV
    deactivate AHS
    deactivate FMS
```

> [!IMPORTANT]
> FMS should begin streaming position updates immediately after sending the activation request, since the position information is required for AVs to activate the escort.

## Typical Escort Activation
AV 1 can adhere to the escort immediately, AV N can not.

```mermaid
sequenceDiagram
    actor Escorter
    participant FMS
    participant AHS
    participant AV 1
    participant AV N

    Note over FMS,AV N: On Connect Sequence
    Escorter->>FMS: Create Escort
    FMS-->>FMS: Escort Pending
    FMS-->+Escorter: Pending
    par AV 1
    FMS->>AHS: ActivateEscortRequestV1
    AHS->>AV 1: Activate Escort
        AV 1->>AV 1: Adhering to Escort
    AV 1->>AHS: Escort Activated
    AHS->>FMS: ActivateEscortResponseV1(status=Activated)
    and AV N
    FMS->>AHS: ActivateEscortRequestV1
    AHS->>AV N: Activate Escort
        Note Over AV N: Unable to immediately adhere to Escort
    AV N->>AHS: Escort Pending
    AHS->>FMS: ActivateEscortResponseV1(status=Pending)
        AV N->>AV N: Adhering to Escort
    AV N->>AHS: Escort Activated
    AHS->>FMS: ActivateEscortResponseV1(status=Activated)
    end

    Escorter-->-FMS: Pending
    Note Over FMS: All AVs activated Escort
    FMS-->>FMS: Escort Activated
    FMS-->>Escorter: Escort Activated
```

> [!TIP]
> The 2-step activation confirmation helps the system avoid unnecessary retries and allows for better transparency to be provided to the user. Status Pending allows the AV to respond to the request immediately, even if it cannot adhere to the request immediately. Status Activate is used to confirm when the request is being adhered to.

## Escort Activation Rejection
When an AV cannot adhere to the request defined in the escort definition, the AHS should send a `"Rejected"` status in the [ActivateEscortResponse](/specification/V1/ActivateEscortResponseV1.md) message to FMS. The FMS will then notify the user accordingly.

> [!NOTE]
> If an AV rejects a given escort, it will never activate it.

```mermaid
sequenceDiagram
    actor Escorter
    participant FMS
    participant AHS
    participant AV 1

    Note over FMS,AV 1: On Connect Sequence
    Escorter->>FMS: Create Escort
    FMS-->>FMS: Escort Pending
    FMS-->+Escorter: Pending
    FMS->>AHS: ActivateEscortRequestV1
    AHS->>AV 1: Activate Escort
    Note Over AV 1: Cannot Adhere to Escort
    AV 1->>AHS: Escort Rejected
    AHS->>FMS: ActivateEscortResponseV1(status=Rejected)
    Escorter-->-FMS: Pending
    FMS-->>FMS: Escort Pending
    FMS-->>Escorter: Error Message
```
