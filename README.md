# ssl-certificate-with-ldap-authorization

password everywhere: passw0rd

commands to create certificates:
openssl genrsa -des3 -out CAPrivate.key 2048
openssl req -x509 -new -nodes -key CAPrivate.key -sha256 -days 3650 -out CAPrivate.pem -subj "/CN=my_certificate_authority/DC=example/DC=com"
openssl genrsa -out ClientA.key 2048
openssl genrsa -out ClientB.key 2048
openssl req -new -key ClientA.key -out ClientA.csr -subj "/CN=ClientA/DC=example/DC=com"
openssl req -new -key ClientB.key -out ClientB.csr -subj "/CN=ClientB/DC=example/DC=com"
openssl x509 -req -in ClientA.csr -CA CAPrivate.pem -CAkey CAPrivate.key -CAcreateserial -out ClientA.crt -days 3650 -sha256
openssl x509 -req -in ClientB.csr -CA CAPrivate.pem -CAkey CAPrivate.key -CAcreateserial -out ClientB.crt -days 3650 -sha256
openssl pkcs12 -export -out ClientA.pfx -inkey ClientA.key -in ClientA.crt
openssl pkcs12 -export -out ClientB.pfx -inkey ClientB.key -in ClientB.crt
keytool -import -file CAPrivate.pem -alias ROOTCA -keystore truststore.jks -storetype jks


openssl genrsa -out Server.key 2048
openssl req -new -key Server.key -out Server.csr -subj "/CN=Server/DC=example/DC=com"
openssl x509 -req -in Server.csr -CA CAPrivate.pem -CAkey CAPrivate.key -CAcreateserial -out Server.crt -days 3650 -sha256
openssl pkcs12 -export -out Server.pfx -inkey Server.key -in Server.crt
keytool -importkeystore -srckeystore Server.pfx -destkeystore keystore.p12 -srcstoretype pkcs12 -deststoretype pkcs12