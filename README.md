Notice: There are 2 ways for a client to access B.

1 set b's public certificate into JDK
2 program the ssl certificate (add client's keystore and trusted store(which include B's public key) into SSLContext)

This example shows how a normal client calls ssl server's API.  (2nd way)

### Key concepts
Key-store: include public and private key
Trust-store: include the public keys which are trusted

### Overview
>
A | A's public key | A's private key | A's key-store (include A's public and private key) | A's trust-store(include B's public key)
>
B | B's public key | B's private key | B's key-store (include B's public and private key) | B's trust-store(include A's public key)

#### Scenario 1:  B provide SSL and anyone can access it
Just add into application.yml with 
server:
  port: 8443
  ssl:
    key-store: classpath:server.jks
    key-store-password: s3cr3t
    key-password: s3cr3t

In this way, anyone can access https://localhost:8443/user from browser

#### Scenario 2:  B provide SSL and just trusted client can access it
>
server:
  port: 8443
  ssl:
    key-store: classpath:server.jks
    key-store-password: s3cr3t
    key-password: s3cr3t
    trust-store: classpath:server.jks
    trust-store-password: s3cr3t
    client-auth: need
    
Only the clients with trusted public key can access B.

#### Try
In this way, anyone can access http://localhost:8080/door?uri=https://localhost:8443/user


#### Scenario 3:  A provide SSL too. Please follow the same settings like B

### Keys generate
1. We can genreate the jks with a single cmd:
> 
keytool -genkeypair -alias serverkey -keyalg RSA -dname "CN=Server,OU=Accenture,O=Development,L=Australia,S=sydney,C=AU" -keypass s3cr3t -keystore c:\Hunter\server.jks -storepass s3cr3t 
>
keytool -genkeypair -alias clientkey -keyalg RSA -dname "CN=Client,OU=Accenture,O=Development,L=Australia,S=sydney,C=AU" -keypass s3cr3t -keystore c:\Hunter\client.jks -storepass s3cr3t

2 Export Client and server certificates
>
keytool -exportcert -alias clientkey -file c:\Hunter\client-public.cer -keystore c:\Hunter\client.jks -storepass s3cr3t 
>
keytool -exportcert -alias serverkey -file c:\Hunter\server-public.cer -keystore c:\Hunter\server.jks -storepass s3cr3t 

3 Import client certificate onto server keystore (and vice versa)
>
keytool -importcert -keystore c:\Hunter\server.jks -alias clientcert -file c:\Hunter\client-public.cer -storepass s3cr3t -noprompt
>
keytool -importcert -keystore c:\Hunter\client.jks -alias servercert -file c:\Hunter\server-public.cer -storepass s3cr3t -noprompt



