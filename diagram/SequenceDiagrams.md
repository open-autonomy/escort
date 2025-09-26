# Escort Sequence Diagrams
The following document describes when the messages should be sent during the lifecycle of the escort messages between the Fleet Management System (FMS), the Autonomous Haulage System (AHS) and the Autonomous Vehicles (AV).

- **[Fleet Synchronization](FleetSynchronization.md)**
    - [On Connect](FleetSynchronization.md#on-connect)
    - [Vehicle Commissioning](FleetSynchronization.md#vehicle-commissioning)
    - [Vehicle Decommissioning](FleetSynchronization.md#vehicle-decommissioning)
- **[Escort Activation](EscortActivation.md)**
    - [Escort Activation with Deadline](EscortActivation.md#escort-activation-deadline-exceed)
    - [Escort Rejection](EscortActivation.md#escort-activate-rejection)
    - [Escort Activation (REST Implementation)](EscortActivation.md#implementation-with-rest-from-fms-to-ahs)
- **[Escort Deactivation](EscortDeactivation.md)**
- **[Escort Updated](EscortUpdated.md)**
- **[Escort Synchronization](Synchronization.md)**
    - [Typical AV Reconnects](Synchronization.md#typical-av-connects)
    - [AV connects With New Pending Escort](Synchronization.md#AV-connects-with-new-pending-escort)
    - [AV connects With Rejection](Synchronization.md#av-connects---reject-active-escorts)