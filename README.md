
Description
============
Example of using RSA keys in apache camel running inside openshift.
This technique differs from the previous in that uses an RSA key mounted into a secret that 
is attached as a configMap/props file 

This example has features two examples:



#1. Instructions for FTP example.
================================
1. create the RSA key and make the public key available to the remote third party.

#2. the create the secret from the private key.
oc create secret generic sftp-private-key --from-file=id_rsa

#3. create the application using 'new-app' - this will create the build and deployment conf. 
oc new-app fuse7-java-openshift:1.2~https://github.com/martincsumner/camel-ftp-example.git --allow-missing-images --strategy=source

#4. create the a config map from application.properties
oc create configmap camel-config --from-file=application.properties

#4.~ look inside the map and you will find the keypair - sftp.privatekey=/etc/ssh/thirdattempt
#This is the name of the rsa key on the virtual mounting path from which it will be presented.

#5. mount the secret into the deployment config.
oc set volume dc/camel-ftp-example --add --mount-path=/etc/ssh/ --secret-name=sftp-private-key

#6. now mount the config map to the same deployment config.
oc set volume dc/camel-ftp-example --add --mount-path=/etc/config/ --configmap-name=camel-config

#7. finally mount set the env. var for spring/camel application to retrieve the rsa key.
oc set env dc/camel-sftp-example SPRING_CONFIG_LOCATION=/etc/config/application.properties



#1. Instructions for SECRETS example.
=====================================


oc create secret generic dbcredentials --from-literal=username=DBUSER  --from-literal=password=DBPASSWORD

oc set volume dc/camel-ftp-example --add --mount-path=/etc/database/ --secret-name=dbcredentials



 


















