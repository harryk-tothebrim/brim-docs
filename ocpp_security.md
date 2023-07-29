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


#### 2.2. Generic Security Profile requirements
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
<br>

#### 2.3. Unsecured Transport with Basic Authentication Profile - 1
Security Profile 1 - Unsecured Transport with Basic Authentication  

<b> Description </b> - The Unsecured Transport with Basic Authentication profile provides a low level of security. Charge Point authentication is done through a username and password. No measures are included to secure the communication channel.  
Read docs for more info - page 7
![fig1](https://github.com/harryk-tothebrim/brim-docs/assets/139219682/963d8876-1ade-473a-b664-d772205b20f1)  
<br>

#### 2.4. TLS with Basic Authentication Profile - 2
Security Profile 2 - TLS with Basic Authentication  

<b> Description </b> - In the TLS with Basic Authentication profile, the communication channel is secured using Transport Layer Security (TLS). The Central System authenticates itself using a TLS server certificate. The Charge Points authenticate themselves using HTTP Basic Authentication.
Read docs for more info - page 9  
![fig2](https://github.com/harryk-tothebrim/brim-docs/assets/139219682/cc564944-e61a-4d21-aca9-dbe0a2e7ed14)  
<br>


#### 2.5. TLS with Client Side Certificates Profile - 3
Security Profile 3 - TLS with Client Side Certificates  

<b> Description </b> - In the TLS with Client Side Certificates profile, the communication channel is secured using Transport Layer Security (TLS). Both the Charge Point and Central System authenticate themselves using certificates.
Read docs for more info - page 9 
![fig3](https://github.com/harryk-tothebrim/brim-docs/assets/139219682/6415aaaf-b3ff-4215-b9a2-7b7f8f2d055d)  
<br>

#### 2.6. Keys used in OCPP
OCPP uses a number of public private key pairs for its security, see below Table. To manage the keys on the Charge Point, messages have been added to OCPP. Updating keys on the Central System or at the manufacturer is out of scope for OCPP. If TLS with Client Side certificates is used, the Charge Point requires a "Charge Point certificate" for authentication against the Central System.  

Table 10. Certificates used in the OCPP security specification  

|CERTIFICATE|PRIVATE KEY STORED AT|DESCRIPTION|
|--|--|--|
Central System Certificate|Central System|Key used to authenticate the Central System.
Central System Root Certificate|Central System|Certificate used to authenticate the Central System.
Charge Point Certificate|Charge Point|Key used to authenticate the Charge Point.|
Firmware Signing Certificate|Manufacturer|Key used to verify the firmware signature.
Manufacturer Root Certificate|Manufacturer|Root certificate for verification of the Manufacturer certificate.
<br>

#### 2.6.2. Certificate Hierarchy
This White Paper adds support for the use of two separate certificate hierarchies:
1. The Charge Point Operator hierarchy which contains the Central System, and Charge Point certificates.
2. The Manufacturer hierarchy which contains the Firmware Signing certificate.
The Central System can update the CPO root certificates stored on the Charge Point using the InstallCertificate.req message.

Table 12. Certificate Hierarchy requirements
|ID|PRECONDITION REQUIREMENT|DEFINITION|
|--|--|--|
|A00.FR.601||The Charge Point Operator MAY act as a certificate authority for the Charge Point Operator hierarchy
|A00.FR.602||The private keys belonging to the CPO root certificates MUST be well protected.
|A00.FR.603||As the Manufacturer is usually a separate organization from the Charge Point Operator, a trusted third party SHOULD be used as a certificate authority. This is essential to have non-repudiation of firmware images.

#### 2.6.3. Certificate Revocation
In some cases a certificate may become invalid prior to the expiration of the validity period. Such cases include changes of the organization name, or the compromise or suspected compromise of the certificate’s private key. In such cases, the certificate needs to be revoked or indicate it is no longer valid. The revocation of the certificate does not mean that the connection needs to be closed as the the connection can stay open longer than 24 hours.  
Different methods are recommended for certificate revocation, see below Table.  
Table 13. Recommended revocation methods for the different certificates.  
|CERTIFICATE|REVOCATION|
|--|--|
|Central System certificate|Fast expiration|
Charge Point certificate|Online verification|
Firmware Signing certificate|Online verification|
<br>

Table 14. Certificate Revocation requirements
|ID|PRECONDITION REQUIREMENT|DEFINITION|
|--|--|--|
|A00.FR.701||Fast expiration SHOULD be used to revoke the Central System certificate. (See Note 1)
|A00.FR.702||The Central System SHOULD use online certificate verification to verify the validity of the Charge Point certificates.
|A00.FR.703||It is RECOMMENDED that a separate certificate authority server is used to manage the certificates.
|A00.FR.704||The Central System SHALL verify the validity of the certificate with the certificate authority server. (See Note 2)
|A00.FR.706||Prior to providing the certificate for firmware validation to the Charge Point, the Central System SHOULD validate both, the certificate and the signed firmware update.|  

<b> Note 1 </b>: With fast expiration, the certificate is only valid for a short period, less than 24 hours. After that the server needs to request a new certificate from the Certificate Authority, which may be the CPO itself (see section Certificate Hierarchy). This prevents the Charge Points from needing to implement revocation lists or online certificate verification. This simplifies the implementation of certificate management at the Charge Point and reduces communication costs at the Charge Point side. By requiring fast expiration, if the certificate is compromised, the impact is reduced to only a short period.
When the certificate chain should becomes compromised, attackers could used forged certificates to trick a Charge Point to connect to a "fake" Central System. By using fast expiration, the time a Charge Point is vulnerable is greatly reduced.
The Charge Point always communicates with the Certificate Authority through the Central System, this way, if the Charge Points is compromised, the Charge Point cannot attack the CA directly.  

<b> Note 2 </b>: This allows for immediate revocation of Charge Point certificates. Revocation of Charge Point certificates will happen for instance when a Charge Point is removed. This is more common than revoking the Central System certificate, which is normally only done when it is compromised.  

<b> Note 3 </b>: It is best practice for any certificate authority server to keep track of revoked certificates.
</br>

#### 2.6.4. Installation during manufacturing or installation.
Unique credentials should be used to authenticate each Charge Point to the Central System, whether they are the password used for HTTP Basic Authentication (see Charge Point Authentication) or the Charge Point certificate. These unique credentials have to be put on the Charge Point at some point during manufacturing or installation.  
Read docs for more info - page 19 
#### A01 - Update Charge Point Password for HTTP Basic Authentication  
Read docs for more info - page 19 
####  A02 - Update Charge Point Certificate by request of Central System  
Read docs for more info - page 21
A03 - Update Charge Point Certificate initiated by the Charge Point  
Read docs for more info - page 25  
A05 - Upgrade Charge Point Security Profile  
Read docs for more info - page 27 
M03 - Retrieve list of available certificates from a Charge Point
Read docs for more info - page 30
M04 - Delete a specific certificate from a Charge Point
Read docs for more info - page 31
M05 - Install CA certificate in a Charge Point
Read docs for more info - page 33  
3. Security events/logging  
Read docs for more info - page 36  
4. Secure firmware update
Read docs for more info - page 41
5. Messages
Read docs for more info - page 48
6. Datatypes
Read docs for more info - page 54
7. Configuration Keys
Read docs for more info - page 62
8. Security Events
Read docs for more info - page 64
9. Changelog Edition 2
Read docs for more info - page 65
10. Changelog Edition 3
Read docs for more info - page 67


