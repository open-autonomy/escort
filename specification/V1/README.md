This document describes Version 1 of the Escort Specification.

# Escort Specification V1
This specification defines the messages and protocols used for managing escorts in Autonomous Haulage System (AHS). It includes the activation, deactivation, synchronization, and handling of escorts to ensure safe operation of the AV.

# Message definition
Find below the Specification for the Version 1 protocol of escorts for Open-Autonomy
- [Policies](#policies)
- [Fleet Synchronization](#fleet-synchronization)
- [Escort Messages](#escort-messages)

# Policies

The following policies are defined in this specification.
- [Exclusion](policies.md#exclusion)	
- [Speed Limit](policies.md#speed-limit)
- [Low Traction](policies.md#low-traction)
- [Rough Road](policies.md#rough-road)

> [!WARNING]
> This list is not exhaustive and more policies can be added in the future.

# Fleet Synchronization
To facilitate escort state synchronization between the FMS and the fleet of AVs managed by the AHS, The AHS must inform the FMS of the current configuration of the fleet. This specification uses the [FleetDefinitionV2](FleetDefinitionV2.md) message from ISO 23725 to exchange fleet definition information between the AHS and FMS.

# Escort Messages

> [!IMPORTANT]
All messages described below must be embedded within the [top-level message header](MessageHeader.md) data structure. 

The following messages are defined in this specification for managing escorts:
- [ActivateEscortRequestV1](ActivateEscortRequestV1.md)
- [ActivateEscortResponseV1](ActivateEscortResponseV1.md)
- [DeactivateEscortRequestV1](DeactivateEscortRequestV1.md)
- [DeactivateEscortResponseV1](DeactivateEscortResponseV1.md)
- [OutOfSyncV1](OutOfSyncV1.md)
- [SyncActiveEscortsRequestV1](SyncActiveEscortsRequestV1.md)
- [SyncActiveEscortsResponseV1](SyncActiveEscortsResponseV1.md)