# Escort 
The Escort feature allows non-instrumented equipment to safely travel in the autonomous operating zone by being escorted by a staffed instrumented vehicle. The non-instrumented equipment is protected by always being inside a zone behind the instrumented vehicle, communicated to all autonomous vehicles.
 
This repository defines the **messages and communication protocol between the Fleet Management System (FMS) and the Autonomous Haulage System (AHS)** for activating and deactivating escorts.
 
## Terminology
| Term | Description |  
| --- | --- |  
| AHS | Autonomous Haulage System |  
| AV | Autonomous Vehicle |  
| FMS | Fleet Management System |  
| AOZ | Autonomous Operating Zone. The area where the autonomus mining vehicles are allowed to operate. |
| SIV | Staffed Instrumented Vehicle. Manually driven equipment equipped with a remote stop. Could be a car, an excavator, or any other type of moving equipment. |
| Escorter | The leading SIV during the escort activity. |
| Escortee | A non-instrumented vehicle being escorted. |
| Escorting convoy | The escorter together with its escortee(s) |
| Escort Protection Zone | The zone in which the escorting convoy is required to be within at all times. Is defined by a configurable distance that extends behind the escorter, to include the escortee. |
| Escort Avoidance Zone | Zone internally defined on each AV, that the AV will not enter. It is at least as big as the protection zone, but is likely bigger due to latency of the escorter's position updates. |

---

## Escort Protection Zone Specification  
Escort protection zones are defined and exchanged using this protocol. The zones are dynamically shaped based on both **escort-defined parameters** and **real-time operating conditions**:

### Parameters
These parameters are part of the activation request, during the active escort phase the parameters are constant througout the entire escort operation.
- **Length**: The Escortee must always stay within _length_ meters of the Escorter along the path that the Escorter is driving.
- **Width**: Configured by the Escorter vehicle, applicable to escorting in open areas. When driving on road the escort must always stay within the lane, and the entire lane width will constitute the protection zone.

![Protection Zone Definition Image](images/escort-protection_zone.png)

### Real-Time Operating Conditions
Once an escort is activated, the Escorter vehicle continuously transmits its position at **1 Hz (one update per second)**. These periodic updates allow AVs to maintain an accurate escort zone. If updates are delayed or missed, the AV will automatically increase its Avoidance Zone to maintain safety margins to the Protection Zone. 
This adaptive expansion ensures AVs keep a protective buffer even when communication is unreliable.  

Each AV will maintain its own representation of the Protection Zone, called Avoidance Zone. This is because an AV must rely on delayed position updates from the Escorter. The AV will use its knowledge about the escort combined with map knowledge to make a prediction on where the escort might have moved during the time delay. This uncertainty due to time delay forces the AV to increase the Avoidance Zone compared to the Protection Zone, in order to maintain safety.

> [!IMPORTANT]
> - The Escortee must at all times stay within the defined Protection Zone.
> - The AV will in its escort predictions assume that an Escort Convoy that is driving on a road will keep driving on the road and within the lane. Therefore it is important that the Escort Convoy actually does that.
---

## Escort Operation State Machine  
Escort zones follow a lifecycle managed through the FMS–AHS communication:  

- `Pending`: The escort operation has been announced but is not yet activated.  
- `Active`: The escort operation is active, transmitting location updates, and enforced by AVs.  
- `Deleted`: The escort operation has been removed and is no longer enforced.  

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
- Escort position updates are streamed at **1 Hz** while the escort is pending or active.  
- If the connection is lost, the following applies:  
  - The connection must be reestablished **as soon as possible**.  
  - During the outage, AVs automatically expand the Avoidance Zone to maintain safety.  
  - Once the connection is restored, the Avoidance Zone contracts back to its configured dimensions.  

---

## Failure Handling  
Because escort zones are safety-critical, the protocol defines explicit behavior for failures:  

- **Connection loss**: Triggers immediate reconnection attempts and automatic safety expansion of the escort zone.  
- **Delayed updates**: Avoidance Zone expands in proportion to the escort’s speed and elapsed time since last update.  
- **Recovery**: Normal zone dimensions are restored as soon as updates resume.  

---

 