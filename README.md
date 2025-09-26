# Escort Zones  
Escort Zones are dynamic safety regions created around an escort vehicle within an autonomous operating area. An escort vehicle is a conventionally driven vehicle equipped with the proper communication system, allowing Autonomous Vehicles (AVs) to detect and respond to its presence.  

This repository defines the **messages and communication protocol between the Fleet Management System (FMS) and the Autonomous Haulage System (AHS)** for activating and deactivating escorts. Through this protocol, AVs in the area enforce a **no-drive zone** around the escort vehicle, ensuring safe movement for non-autonomous or non-equipped vehicles.  

---

### Language  
| Acronyms | Extended Name |  
| --- | --- |  
| AHS | Autonomous Haulage System |  
| AV | Autonomous Vehicle |  
| FMS | Fleet Management System |  

---

## Escort Zone Specification  
Escort zones are defined and exchanged using this protocol. The zones are dynamically shaped based on both **escort-defined parameters** and **real-time operating conditions**:  

- **Width**: Configured by the escorting vehicle, but constrained by the lane width.  
- **Length**: Initially defined by the escort, but may extend depending on communication latency and the escort’s speed.  
  - If position updates are immediate, the zone length equals the escort vehicle length.  
  - If updates are delayed, the zone expands in proportion to the elapsed time and the escort’s speed.  

> [!IMPORTANT]  
> Once an escort is activated, the escort vehicle continuously transmits its position at **1 Hz (one update per second)**. These periodic updates allow AVs to maintain an accurate escort zone. If updates are delayed or missed, the zone length automatically increases to maintain safety margins.  

This adaptive expansion ensures AVs keep a protective buffer even when communication is unreliable.  

---

## Escort Zone State Machine  
Escort zones follow a lifecycle managed through the FMS–AHS communication:  

- `Pending`: The escort zone has been created but not yet activated.  
- `Active`: The escort zone is active, transmitting location updates, and enforced by AVs.  
- `Deleted`: The escort zone has been removed and is no longer enforced.  

![Escort Zone State Machine](draw.io/EscortZoneStateMachine.svg)  

> [!NOTE]  
> Immutable attributes (e.g., escort vehicle ID, Station ID (V2X ID), communication configuration) cannot be changed on an active zone. To update them, a new escort zone must be created, and the old one must be retired.  

---

## Sequence Diagrams  
See [Sequence Diagrams](diagram/SequenceDiagrams.md) for scenarios illustrating the interactions between FMS, AHS, and AV when creating, activating, updating, and removing escort zones.  

---

## Communication Protocol  
All communication between the FMS and AHS for managing escort zones is handled through a **single persistent WebSocket connection**.  

This connection must satisfy the following requirements:  
- The connection is continuously monitored from both ends.  
- Messages are exchanged asynchronously over the connection.  
- Escort position updates are streamed at **1 Hz** while the escort is active.  
- If the connection is lost, the following applies:  
  - The connection must be reestablished **as soon as possible**.  
  - During the outage, AVs automatically expand the escort zone length to maintain safety.  
  - Once the connection is restored, the zone contracts back to its configured dimensions.  

---

## Failure Handling  
Because escort zones are safety-critical, the protocol defines explicit behavior for failures:  

- **Connection loss**: Triggers immediate reconnection attempts and automatic safety expansion of the escort zone.  
- **Delayed updates**: Zone length expands in proportion to the escort’s speed and elapsed time since last update.  
- **Recovery**: Normal zone dimensions are restored as soon as updates resume.  

---

 