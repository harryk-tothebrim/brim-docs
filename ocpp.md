### OCPP version 1.6 - Brief Overview
#### 1. Scope
This documentation defines the protocol used between a Charge Point and Central System. If the protocol requires a certain action or response from one side or the other, then this will be stated in this documentation.
The specification does not define the communication technology. Any technology will do, as long as it supports TCP/IP connectivity.  
<br>  

#### 2.2. Definitions
This section contains the terminology that is used throughout this document.
| terminology | meaning |
| --- | --- |
 |  Central System | Charge Point Management System: the central system that manages Charge Points and has the information for authorizing users for using its Charge Points.
| CiString | Case Insensitive String. Only printable ASCII allowed.
| Charge Point | The Charge Point is the physical system where an electric vehicle can be charged. A Charge Point has one or more connectors.
| Charging Profile | Generic Charging Profile, used for different types of Profiles. Contains information about the Profile and holds the Charging Schedule. In future versions of OCPP it might hold more than 1 Charging Schedule.
| Charging Schedule | Part of a Charging Profile. Defines a block of charging Power or Current limits. Can contain a start time and length.
| Charging Session | A Charging Session is started when first interaction with user or EV occurs. This can be a card swipe, remote start of transaction, connection of cable and/or EV, parking bay occupancy detector, etc.
| Composite Charging Schedule |The charging schedule as calculated by the Charge Point. It is the result of the calculation of all active schedules and possible local limits present in the Charge Point. Local Limits might be taken into account.
| Connector |The term “Connector”, as used in this specification, refers to an independently operated and managed electrical outlet on a Charge Point. This usually corresponds to a single physical connector, but in some cases a single outlet may have multiple physical socket types and/or tethered cable/connector arrangements to facilitate different vehicle types (e.g. four-wheeled EVs and electric scooters).
| Control Pilot signal |Signal used by a Charge Point to inform EV of maximum Charging power or current limit, as defined by [IEC61851-1].
| Energy Offer Period |Energy Offer Period starts when the EVSE is ready and willing to supply energy.
| Energy Offer SuspendPeriod |During a transaction, there may be periods the EnergyOffer to EV is suspended by the EVSE, for instance due to Smart Charging or local balancing.
| Energy Transfer Period | Time during which an EV chooses to take offered energy, or return it. Multiple Energy Transfer Periods are possible during a Transaction.
| Local Controller | Optional device in a smart charging infrastructure. Located on the premises with a number of Charge Points connected to it. Sits between the Charge Points and Central System. Understands and speaks OCPP messages. Controls the Power or Current in other Charge Point by using OCPP smart charging messages. Can be a Charge Point itself.
| OCPP-J | OCPP via JSON over WebSocket
| OCPP-S | OCPP via SOAP
| Phase Rotation | Defines the wiring order of the phases between the electrical meter (or if absent, the grid connection), and the Charge Point connector.
| Transaction | The part of the charging process that starts when all relevant preconditions (e.g. authorization, plug inserted) are met, and ends at the moment when the Charge Point irrevocably leaves this state.
| String | Case Sensitive String. Only printable ASCII allowed. All strings in messages and enumerations are case sensitive, unless explicitly stated otherwise.
<br>  

2.3. Abbreviations
| abv | meaning |
| --- | --- |
| CSL | Comma Separated List
| CPO | Charge Point Operator
| DNS | Domain Name System
| DST | Daylight Saving Time
| EV | Electrical Vehicle, this can be BEV (battery EV) or PHEV (plug-in hybrid EV)
| EVSE | Electric Vehicle Supply Equipment [IEC61851-1]
| FTP(S) | File Transport Protocol (Secure)
| HTTP(S) | HyperText Transport Protocol (Secure)
| ICCID | Integrated Circuit Card Identifier
| IMSI | International Mobile Subscription Identity
| JSON | JavaScript Object Notation
| NAT | Native Address Translation
| PDU | Protocol Data Unit
| SC | Smart Charging
| SOAP | Simple Object Access Protocol
| URL | Uniform Resource Locator
| RST | 3 phase power connection, Standard Reference Phasing
| RTS | 3 phase power connection, Reversed Reference Phasing
| SRT | 3 phase power connection, Reversed 240 degree rotation
| STR | 3 phase power connection, Standard 120 degree rotation
| TRS | 3 phase power connection, Standard 240 degree rotation
| TSR | 3 phase power connection, Reversed 120 degree rotation
| UTC | Coordinated Universal Time  
<br>


#### 3. Introduction
This is the specification for OCPP version 1.6.  

OCPP is a standard open protocol for communication between Charge Points and a Central System and is designed to accommodate any type of charging technique.
OCPP 1.6 introduces new features to accommodate the market: Smart Charging, OCPP using JSON over Websockets, better diagnostics possibilities (Reason),
more Charge Point Statuses and TriggerMessage.  

Some basic concepts are explained in the sections in the ocpp1.6 documentation introductory chapter. 
The chapters: Operations Initiated by Charge Point and Operations Initiated by Central System describe the operations supported by the protocol. 
The exact messages and their parameters are detailed in the chapter: Messages and data types are described in chapter: Types. 
Defined configuration keys are described in the chapter: Standard Configuration Key Names & Values.  

In OCPP 1.6 features and associated messages are grouped in profiles. 
Depending on the required functionality, implementers can choose to implement one or more of the following profiles.

|PROFILE NAME  |  DESCRIPTION  |
| --- | --- |
|  Core  |  Basic Charge Point functionality comparable with OCPP 1.5 [OCPP1.5] without support for firmware updates, local authorization list management and reservations.
|  Firmware Management  |  Support for firmware update management and diagnostic log file download.
|  Local Auth List Management  |  Features to manage the local authorization list in Charge Points.
|  Reservation  |  Support for reservation of a Charge Point.
|  Smart Charging  |  Support for basic Smart Charging, for instance using control pilot.
|  Remote Trigger  |  Support for remote triggering of Charge Point initiated messages

* refer to page 9 of docs for further clarity on table above
<br> 


#### General views of operation
The following figures describe the general views of the operations between Charge Point and Central System for two cases:
1. a Charge Point requesting authentication of a card and sending charge transaction status
2. Central System requesting a Charge Point to update its firmware.
The arrow labels in the following figures indicate the PDUs exchanged during the invocations of the operations. These PDUs are defined in detail in the Messages section.

![fig1_seq_diagram](https://github.com/harryk-tothebrim/brim-docs/assets/139219682/cb33987f-767f-412d-8ef8-0bf4ed9cffbb)  

When a Charge Point needs to charge an electric vehicle, it needs to authenticate the user first before the charging can be started. If the user is authorized the Charge Point informs the Central System that it has started with charging. 

When a user wishes to unplug the electric vehicle from the Charge Point, the Charge Point needs to verify that the user is either the one that initiated the charging or that the user is in the same group and thus allowed to terminate the charging. Once authorized, the Charge Point informs the Central System that the charging has been stopped.  
> A Charge Point MUST NOT send an Authorize.req before stopping a transaction if the presented idTag is the same as the idTag presented to start the transaction. 
<br>

![fig2](https://github.com/harryk-tothebrim/brim-docs/assets/139219682/e18e5ca9-ea8d-40d8-be08-39719f22928c)  

When a Charge Point needs to be updated with new firmware, the Central System informs the Charge Point of the time at which the Charge Point can start downloading the new firmware. The Charge Point SHALL notify the Central System after each step as it downloads and installs the new firmware.

<br>

#### 3.5 Local Authorization & Offline Behavior  
In the event of unavailability of the communications or even of the Central System, the Charge Point is designed
to operate stand-alone. In that situation, the Charge Point is said to be offline.
To improve the experience for users, a Charge Point MAY support local authorization of identifiers, using an
Authorization Cache and/or a Local Authorization List.
This allows (a) authorization of a user when offline, and (b) faster (apparent) authorization response time when
communication between Charge Point and Central System is slow.  
For more info, refer to docs - page 12  


#### 3.5.1 Authorization Cache
A Charge Point MAY implement an Authorization Cache that autonomously maintains a record of previously presented identifiers that have been successfully authorized by the Central System. (Successfully meaning: a response received on a message containing an idTag)  
For more info, refer to docs - page 13  

#### 3.5.2 Local Authorization List
The Local Authorization List is a list of identifiers that can be synchronized with the Central System.
The list contains the authorization status of all (or a selection of) identifiers and the authorization status/expiration date.
For more info, refer to docs - page 13  

#### 3.5.3. Relation between Authorization Cache and Local Authorization List
The Authorization Cache and Local Authorization List are distinct logical data structures. Identifiers known in the Local Authorization List SHALL NOT be added to the Authorization Cache.
Where both Authorization Cache and Local Authorization List are supported, a Charge Point SHALL treat Local Authorization List entries as having priority over Authorization Cache entries for the same identifiers.  
<br>

#### 3.5.4. Unknown Offline Authorization
When offline, a Charge Point MAY allow automatic authorization of any "unknown" identifiers that cannot be explicitly authorized by Local Authorization List or Authorization Cache entries. Identifiers that are present in a Local Authorization List that have a status other than “Accepted” (Invalid, Blocked, Expired) MUST be rejected. Identifiers that were valid but are apparently expired due to passage of time MUST also be rejected.  
For more info, refer to docs - page 14  
<br>

#### 3.6. Transaction in relation to Energy Transfer Period
The Energy Transfer Period is a period of time during wich energy is transferred between the EV and the EVSE.  
For more info, refer to docs - page 15  
<br>

#### 3.7. Transaction-related messages
The Charge Point SHOULD deliver transaction-related messages to the Central System in chronological order as soon as possible. Transaction-related messages are StartTransaction.req, StopTransaction.req and periodic or clock-aligned MeterValues.req messages.
When offline, the Charge Point MUST queue any transaction-related messages that it would have sent to the Central System if the Charge Point had been online.  

In the event that a Charge Point has transaction-related messages queued to be sent to the Central System, new messages that are not transaction-related MAY be delivered immediately without waiting for the queue to be emptied. It is therefore allowed to send, for example, an Authorize request or a Notifications request before the transaction-related message queue has been emptied, so that customers are not kept waiting and urgent notifications are not delayed.  

The delivery of new transaction-related messages SHALL wait until the queue has been emptied. This is to ensure that transaction-related messages are always delivered in chronological order.  

When the Central System receives a transaction-related message that was queued on the Charge Point for some time, the Central System will not be aware that this is a historical message, other than by inference given that the various timestamps are significantly in the past. It SHOULD process such a message as any other.  

#### 3.7.1. Error responses to transaction-related messages
It is permissible for the Charge Point to skip a transaction-related message if and only if the Central System repeatedly reports a `failure to process the message'. Such a stipulation is necessary, because otherwise the requirement to deliver every transaction-related message in chronological order would entail that the Charge Point cannot deliver any transaction-related messages to the Central System after a software bug causes the Central System not to acknowledge one of the Charge Point’s transaction-related messages.  

What kind of response, or failure to respond, constitutes a `failure to process the message' is defined in the documents OCPP JSON Specification and OCPP SOAP Specification.  
For more info, refer to docs - page 17  
<br>

#### 3.8. Connector numbering
To enable Central System to be able to address all the connectors of a Charge Point, ConnectorIds MUST always be numbered in the same way.  
For more info, refer to docs - page 17  
<br>

#### 3.9. ID Tokens
For more info, refer to docs - page 18  
<br>

#### 3.10. Parent idTag
For more info, refer to docs - page 19  
<br>  

####  3.11. Reservations
For more info, refer to docs - page 20  
<br>  



