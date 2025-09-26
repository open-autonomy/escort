# Escort Activation
When an escort is created, the Fleet Management System (FMS) initiates the activation process by sending `ActivateEscortRequestV1` messages to the Autonomous Haulage System (AHS) for each of the Autonomous Vehicles (AVs) defined in the Fleet Definition. The AHS then communicates with each of the AVs to activate the escort internally. This document now also includes the full life cycle: activation attempts, steady‑state position pulsing, and deactivation.

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
    AHS->>AV: Activate
    AV->>AV: Protection zone pending
    AV-->>AHS: Accepted
    AHS-->>FMS: ActivateEscortResponseV1(status=Accepted)

    AV->>AV: Enable protection zone
    AV-->>AHS: Activated
    AHS-->>FMS: ActivateEscortResponseV1(status=Activated)
    FMS-->>FMS: All AVs Activated
    FMS-->>Escorter: Escort activated
    Note over FMS: DO NOT wait for response<br/>to start sending EscortPosition
    loop Escort loop (1 Hz)
        Escorter-->>FMS: (Position Source)
        FMS-->>AHS: EscortPosition
        AHS->>AV: PositionUpdate
        AV->>AV: Update zone geometry
        AV-->>AHS: Updated
    end
    %% Deactivation flow moved to EscortDeactivation.md
    deactivate AV
    deactivate AHS
    deactivate FMS
```

> [!TIP]
> FMS must begin streaming position updates immediately after sending the activation request; waiting for an Accepted / Activated response can reduce safety margins.

## Typical Escort Activation

```mermaid
sequenceDiagram
    participant User
    participant FMS
    participant AHS
    participant AV 1
    participant AV N

    Note over FMS,AV N: On Connect Sequence
    User->>FMS: Create Escort
    FMS-->>FMS: Escort Pending
    FMS-->+User: Pending
    par AV 1
    FMS->>AHS: ActivateEscortRequestV1
    AHS->>AV 1: ActivateEscortCommand
    AV 1->>AHS: ActivationStatus(accepted)
    AHS->>FMS: ActivateEscortResponseV1(status=Accepted)
        AV 1->>AV 1: Adheres to request
    AV 1->>AHS: ActivationStatus(activated)
    AHS->>FMS: ActivateEscortResponseV1(status=Activated)
    and AV N
    FMS->>AHS: ActivateEscortRequestV1
    AHS->>AV N: ActivateEscortCommand
    AV N->>AHS: ActivationStatus(accepted)
    AHS->>FMS: ActivateEscortResponseV1(status=Accepted)
        Note Over AV N: Unable to immediately adhere to request
        AV N->>AV N: Adheres to request
    AV N->>AHS: ActivationStatus(activated)
    AHS->>FMS: ActivateEscortResponseV1(status=Activated)
    end

    User-->-FMS: Pending
    Note Over FMS: All AVs activated Escort
    FMS-->>FMS: Escort Activated
    FMS-->>User: Escort Activated
```

> [!TIP]
> The 2-step activation confirmation helps the system avoid unnecessary retries and allows for better transparency to be provided to the user. Accept allows the AV to respond to the request immediately, even if it cannot adhere to the request immediately. Activate is used to confirm when the request is being adhered to.

## Escort Activation Rejection
When an AV cannot adhere to the request defined in the escort definition, the AHS should send a `"Rejected"` status in the `ActivateEscortResponse` message to FMS. The FMS will then notify the user accordingly.

> [!NOTE]
> If an AV rejects the `ActivateEscortRequestV1` message for a given escort, the escort will not be activated within the FMS and will remain as `"pending"` until all AVs have successfully activated the escort.

```mermaid
sequenceDiagram
    participant User
    participant FMS
    participant AHS
    participant AV 1

    Note over FMS,AV 1: On Connect Sequence
    User->>FMS: Create Escort
    FMS-->>FMS: Escort Pending
    FMS-->+User: Pending
    FMS->>AHS: ActivateEscortRequestV1
    AHS->>AV 1: ActivateEscortCommand
    Note Over AV 1: Cannot Adhere to request
    AV 1->>AHS: ActivationStatus(rejected)
    AHS->>FMS: ActivateEscortResponseV1(status=Rejected)
    User-->-FMS: Pending
    FMS-->>FMS: Escort Pending
    FMS-->>User: Error Message
```