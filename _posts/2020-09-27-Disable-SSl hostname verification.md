---
published: true
---
when we open an SSL connection in Java the default implementation of the SSL protocol performs below validations to ensure the requested host is not fake:
---It performs the ssl handshake.
---During the handshake it validates if the server certificate is available in the JVMs truststore(by 		default cacerts is the JVM truststore).
---once the valid certificate is found in the JVM truststore during the handshake. The validation of the 	Hostname verification takes place based on the "**CN**" and "**SAN**" entries available in the Server 		certificate.




