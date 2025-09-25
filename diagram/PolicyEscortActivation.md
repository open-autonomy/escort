# Policy Escort Activation
When a policy escort is created, the Fleet Management System (FMS) initiates the activation process by sending `ActivateEscortRequestV1` messages to the Autonomous Haulage System (AHS) for each of the Autonomous Vehicle (AV)s defined in the Fleet Definition. The AHS then communicates with each of the AVs to activate the policy escort internally. The following sequence diagram illustrates this process.

> [!IMPORTANT]
> All systems shall implement idempotency when managing Policy Escort Activations.

## Typical Policy Escort Activation

```mermaid
sequenceDiagram
    participant User
    participant FMS
    participant AHS
    participant AV 1
    participant AV N

    Note over FMS,AV N: On Connect Sequence
    User->>FMS: Create Policy Escort
    FMS-->>FMS: Policy Escort Pending
    FMS-->+User: Pending
    par AV 1
        FMS->>AHS: Send ActivateEscortRequestV1 to AV 1
        AHS->>AV 1: Activate Policy Escort
        AV 1->>AHS: Accept
        AHS->>FMS: ActivateEscortResponseV1: Status "Accepted"
        AV 1->>AV 1: Adheres to Policy
        AV 1->>AHS: Activated
        AHS->>FMS: ActivateEscortResponseV1: Status "Activated"
    and AV N
        FMS->>AHS: Send ActivateEscortRequestV1 to AV N
        AHS->>AV N: Activate Policy Escort
        AV N->>AHS: Accept
        AHS->>FMS: ActivateEscortResponseV1: Status "Accepted"
        Note Over AV N: Unable to immediately adhere to policy
        AV N->>AV N: Adheres to Policy
        AV N->>AHS: Activated
        AHS->>FMS: AV N <br/> ActivateEscortResponseV1: Status "Activated"
    end

    User-->-FMS: Pending
    Note Over FMS: All AVs activated Policy Escort
    FMS-->>FMS: Policy Escort Activated
    FMS-->>User: Policy Escort Activated
```

> [!TIP]
> The 2-step activation confirmation helps the system avoid unnecessary retries and allows for better transparency to be provided to the user. Accept allows the AV to respond to the request immediately, even if it cannot adhere to the policy immediately. Activate is used to confirm when the policy is being adhered to.

## Policy Escort Activation Deadline Exceed
The policy escort can be created with the `activationDealine` property. This field is an indicative field that lets the AV know it should start to adhere to the policy if possible. However, it is not a strict demand and the AV is allowed to defer compliance up until the specified time. Once the activation deadline is reached, the AV must make best effort to adhere to the policy. Once the AV is adhereing to the policy, it should send its policy Acivation response.

> [!NOTE]
> The inclusion of the activation deadline does not change the activation process from the perspective of the FMS. For the FMS to consider the escort active, the FMS still needs to receive confirmation from all AVs that the escort has been activated.

> [!TIP]
> Activation deadlines let the user create a escorts as 'exit only' while the escort is transitioning from Pending to Active. This helps reduce the need to manually manage traffic during the transition stage when creating a escort.

```mermaid
sequenceDiagram
    participant User
    participant FMS
    participant AHS
    participant AV 1

    Note over FMS,AV 1: On Connect Sequence
    User->>FMS: Create Policy Escort <br/> with Activation Deadline
    FMS-->>FMS: Policy Escort Pending
    FMS-->+User: Pending
    FMS->>AHS: Send ActivateEscortRequestV1 to AV 1
    AHS->>AV 1: Activate Policy Escort

    Note Over AV 1: Unable to immediately adhere to policy
    AV 1->>AHS: Accept
    AHS->>FMS: ActivateEscortResponseV1: Status "Accepted"

    Note Over AV 1: Activation Dealine Exceeded
    Note Over AV 1: Proceed to Adheres to Policy ...
    Note Over AV 1: Adhering to Policy
    AV 1->>AV 1: Adheres to Policy
    AV 1->>AHS: Activated

    AHS->>FMS: ActivateEscortResponseV1: Status "Activated"
    User-->-FMS: Pending
    Note Over FMS: All AVs activated Policy Escort
    FMS-->>FMS: Policy Escort Activated
    FMS-->>User: Policy Escort Activated
```

## Policy Escort Activate Rejection
When an AV cannot adhere to the policy defined in the policy escort definition, the AHS should send a `"Rejected"` status in the `ActiveEscortResponse` message to FMS. The FMS will then notify the user accordingly.

> [!NOTE]
> If an AV rejects the `ActivateEscortRequestV1` message for a given escort, the escort will not be activated within the FMS and will remain as `"pending"` until all AVs have successfully activated the escort.

```mermaid
sequenceDiagram
    participant User
    participant FMS
    participant AHS
    participant AV 1

    Note over FMS,AV 1: On Connect Sequence
    User->>FMS: Create Policy Escort
    FMS-->>FMS: Policy Escort Pending
    FMS-->+User: Pending
    FMS->>AHS: Send ActivateEscortRequestV1 to AV 1
    AHS->>AV 1: Activate Policy Escort
    Note Over AV 1: Cannot Adhere to policy
    AV 1->>AHS: Rejected
    AHS->>FMS: ActivateEscortResponseV1 (Escort 1): Status "Rejected"
    User-->-FMS: Pending
    FMS-->>FMS: Policy Escort Pending
    FMS-->>User: Error Message
```