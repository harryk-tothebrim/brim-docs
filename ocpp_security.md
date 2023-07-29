The white paper(OCPP Security Ed. WhitePaper) describes how the security enhancements, introduced in OCPP 2.0, can be used, on top of OCPP 1.6-J, in a standardized way.

#### 1.3 Security Objectives

OCPP security has been designed to meet the following security objectives:
1. To allow the creation of a secure communication channel between the Central System and Charge Point. The integrity and confidentiality of messages on this channel should be protected with strong cryptographic measures.
2. To provide mutual authentication between the Charge Point and the Central System. Both parties should be able to identify who they are communicating with.
3. To provide a secure firmware update process by allowing the Charge Point to check the source and the integrity of firmware images, and by allowing non-repudiation of these images.
4. To allow logging of security events to facilitate monitoring the security of the smart charging system.
<br>

#### 1.4. OCPP-J Only
This document is for OCPP 1.6-J (JSON over WebSockets) only, OCPP-S (SOAP) is NOT supported. This document was started, as it is seen as a simple step to port OCPP 2.0 security to OCPP 1.6. But as OCPP 2.0/2.0.1 only supports JSON over WebSockets (not SOAP), this document is also written for OCPP 1.6-J only. Adding SOAP to this document would have taken a lot of work and review by security experts.
<br>

### 2. Secure connection setup 
#### 2.1. Security Profiles
This section defines the different OCPP security profiles and their requirement. This White Paper supports three security profiles:
The table below shows which security measures are used by which profile.  
<em> Table 2. Overview of OCPP security profiles </em>

| PROFILE | CHARGE POINT AUTHENTICATION | CENTRAL SYSTEM AUTHENTICATION | COMMUNICATION SECURITY |
|--|--|--|--|
|1. Unsecured Transport with Basic Authentication | HTTP Basic Authentication |  | | 
|2. TLS with Basic Authentication | HTTP Basic Authentication | TLS authentication using certificate | Transport Layer Security (TLS) | 
|3. TLS with Client Side Certificates | TLS authentication using certificate | TLS authentication using certificate | Transport Layer Security (TLS) | 

• The Unsecured Transport with Basic Authentication Profile does not include authentication for the Central System, or measures to set up a secure communication channel. Therefore, it should only be used in trusted networks, for instance in networks where there is a VPN between the Central System and the Charge Point. For field operation it is highly recommended to use a security profile with TLS.


2.2. Generic Security Profile requirements
Table 3. Generic Security Profile requirements
|ID|PRECONDITION|REQUIREMENT DEFINITION|
|--|--|--|
A00.FR.001||The Charge Point and Central System SHALL only use one security profile at a time|
A00.FR.002|If the Charge Point tries to connect with a different profile than the Central System is using|The Central System SHALL reject the connection.|
A00.FR.003|If the Charge Point detects that the Central System has accepted a connection with a different profile than the Charge Point is using|The Charge Point SHALL terminate the connection.
A00.FR.004||The security profile SHALL be configured before OCPP communication is enabled.|
A00.FR.005||Lowering the security profile that is used to a less secure profile is, for security reasons, not part of the OCPP specification, and MUST be done through another method, not via OCPP. OCPP messages SHALL NOT be used for this (e.g. ChangeConfiguration.req or DataTransfer).
A00.FR.006|When a Central System communicates with Charge Points with different security profiles or different versions of OCPP.|The Central System MAY operate the Charge Points via different addresses or ports of the Central System.  For instance, the Central System server may have one TCP port for TLS with Basic Authentication, and another port for TLS with Client Side Certificates.  In this case there is only one security profile in use per port of the Central System, which is allowed.|  

> <b> NOTE </b> -
Only securing the OCPP communication is not enough to build a secure Charge Point. All other interfaces to the Charge Point should be equally well secured.
