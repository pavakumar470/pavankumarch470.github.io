---
published: true
---
Below are the steps while performing the SSL Handshake:<br/>
1.cleint sends the Hello message and the message conatins the "session ID","Cipher Suits","Compression methods" and "signature methods"<br/>
2.and then server sends the random "session ID" and selected "Cipher suit" as well.<br/>
3.server sends the certificates chain that is being used by the server.<br/>
4.client sends the message with handshake complete or "un known certificate" if the public certs is not available in client truststore


<br/><br/>

For debugging the issue with the below error we might not know which certificate is missing we need to get the handshake details while the java program connecting to the server that is ssl enabled:<br/>
```java
javax.net.ssl.SSLHandshakeException: sun.security.validator.ValidatorException: PKIX path building failed: sun.security.provider.certpath.SunCertPathBuilderException: unable to find valid certification path to requested target
```
<br/>
when you see the above error we can run the Java program with the "-Djavax.net.debug=ssl,handshake" JVM option which will capture all the handshake related details with ssl. with that you can find the certificate chain that has been sent to the client by server during the handshake process and if the program fails with the above mentioned error with the JVM option you can search for the "certificate_unknown" message and above that message we can check for the certifcate that server is using and missing the client truststore.