FROM mdelapenya/liferay-portal:6.2-ce-ga6-tomcat-hsql

COPY ./configs/portal-ext.properties $LIFERAY_HOME/portal-ext.properties

ENTRYPOINT ["catalina.sh", "run"]

