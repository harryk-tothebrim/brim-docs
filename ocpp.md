### OCPP version 1.6 - Brief Overview
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






