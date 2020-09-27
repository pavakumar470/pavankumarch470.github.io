---
published: true
---
When we open the ssl connection in java. The defualt SSL implemetation performs the below validation steps to ensure that the requested host is not fake:
1.It performs the ssl handshake where the server sends its certificates.
2.During the ssl handshake it verifies if the server certificate is available in the JVMs truststore(by defualt CACERTS is the JVM truststore)
3.Once the valid certificate is found in the JVM truststore during the handshake. The validation of the Hostname Verification takes place based on the "**CN**" and "**SAN**" enteries available in the server certiicates.

For example if the certificates are having the below as the "**Subject Alternative Name(SAN)**"

