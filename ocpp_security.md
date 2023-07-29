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
