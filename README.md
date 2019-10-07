

oc create secret generic sftp-private-key --from-file=id_rsa

oc new-app fuse7-java-openshift:1.2~https://github.com:martincsumner/camel-ftp-example.git --allow-missing-images --strategy=source

oc create configmap camel-config --from-file=application.properties

oc set volume dc/camel-sftp-example --add --mount-path=/etc/ssh/ --secret-name=sftp-private-key

oc set volume dc/camel-sftp-example --add --mount-path=/etc/config/ --configmap-name=camel-config

oc set env dc/camel-sftp-example SPRING_CONFIG_LOCATION=/etc/config/application.properties