---
published: true
---
when we open an SSL connection in Java the default implementation of the SSL protocol performs below validations to ensure the requested host is not fake:<br/>
1.It performs the ssl handshake.<br/>
2.During the handshake it validates if the server certificate is available in the JVMs truststore(by 		default cacerts is the JVM truststore).<br/>
3.once the valid certificate is found in the JVM truststore during the handshake. The validation of the 	Hostname verification takes place based on the "**CN**" and "**SAN**" entries available in the Server 		certificate.<br/>

For Example if the server certificate is having the below as the "Subject Alternate Name(SAN)" entries:<br/>
```
	SubjectAlternativeName [
    	DNSName: inchVM.domain.com
     	DNSName: inkumarVM.domain.com
     	IPAddress: 127.0.0.1
     	]
```
