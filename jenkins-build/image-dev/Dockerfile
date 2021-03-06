FROM mdelapenya/jdk:8-openjdk
MAINTAINER Yousri BENDIABDALLAH <b.yousri@liferay.com>

ENV LIFERAY_HOME=/liferay
ENV LIFERAY_SHARED=/storage/liferay
ENV LIFERAY_CONFIG_DIR=/tmp/liferay/configs
ENV LIFERAY_DEPLOY_DIR=/tmp/liferay/deploy
ENV CATALINA_HOME=$LIFERAY_HOME/tomcat-8.0.32
ENV PATH=$CATALINA_HOME/bin:$PATH
ENV LIFERAY_TOMCAT_URL=https://sourceforge.net/projects/lportal/files/Liferay%20Portal/7.0.5%20GA6/liferay-ce-portal-tomcat-7.0-ga6-20180320170724974.zip/download
ENV GOSU_VERSION 1.10
ENV GOSU_URL=https://github.com/tianon/gosu/releases/download/$GOSU_VERSION

WORKDIR /usr/local


RUN apt-get update \
    && apt-get install -y curl tree \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* \
    && useradd -ms /bin/bash liferay \
    && gpg --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys B42F6819007F00F88E364FD4036A9C25BF357DD4 \
    && curl -o /usr/local/bin/gosu -SL "https://github.com/tianon/gosu/releases/download/1.2/gosu-amd64" \
    && curl -o /usr/local/bin/gosu.asc -SL "https://github.com/tianon/gosu/releases/download/1.2/gosu-amd64.asc" \
    && gpg --verify /usr/local/bin/gosu.asc \
    && rm /usr/local/bin/gosu.asc \
    && rm -r /root/.gnupg/ \
    && chmod +x /usr/local/bin/gosu \
    && mkdir -p "$LIFERAY_HOME" \
    && set -x \
    && curl -fSL "$LIFERAY_TOMCAT_URL" -o /tmp/liferay-ce-portal-tomcat-7.0-ga6-20180320170724974.zip \
    && unzip /tmp/liferay-ce-portal-tomcat-7.0-ga6-20180320170724974.zip -d /tmp/liferay \
    && mv /tmp/liferay/liferay-ce-portal-7.0-ga6/* $LIFERAY_HOME/ \
    && rm /tmp/liferay-ce-portal-tomcat-7.0-ga6-20180320170724974.zip \
    && rm -fr /tmp/liferay/liferay-ce-portal-7.0-ga6 \
    && chown -R liferay:liferay $LIFERAY_HOME \
    && export GNUPGHOME="$(mktemp -d)" 

COPY ./configs/setenv.sh $CATALINA_HOME/bin/setenv.sh
COPY ./entrypoint.sh /usr/local/bin
COPY ./configs/ $LIFERAY_CONFIG_DIR
RUN chmod +x /usr/local/bin/entrypoint.sh

EXPOSE 8080/tcp
EXPOSE 9000/tcp
EXPOSE 11311/tcp

VOLUME /storage

ENTRYPOINT ["entrypoint.sh"]
CMD ["catalina.sh", "run"]




