FROM mdelapenya/jdk:8-openjdk
MAINTAINER Yousri BENDIABDALLAH <b.yousri@liferay.com>

RUN apt-get update \
  && apt-get install -y curl tree \
  && apt-get clean \
  && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* \
  && useradd -ms /bin/bash liferay

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




RUN apt-get install -y ca-certificates file openssl

ENV RUNC_VERSION v0.1.0

RUN mkdir -p /go/src/github.com/opencontainers \
	&& wget -O- "https://github.com/opencontainers/runc/archive/${RUNC_VERSION}.tar.gz" \
		| tar -xzC /go/src/github.com/opencontainers \
	&& mv "/go/src/github.com/opencontainers/runc-${RUNC_VERSION#v}" /go/src/github.com/opencontainers/runc

ENV GOPATH $GOPATH:/go/src/github.com/opencontainers/runc/Godeps/_workspace

# disable CGO for ALL THE THINGS (to help ensure no libc)
ENV CGO_ENABLED 0

ENV BUILD_FLAGS="-v -ldflags '-d -s -w'"

COPY *.go /go/src/github.com/tianon/gosu/
WORKDIR /go/src/github.com/tianon/gosu

# gosu-$(dpkg --print-architecture)
RUN set -x \
	&& eval "GOARCH=amd64 go build $BUILD_FLAGS -o /go/bin/gosu-amd64" \
	&& file /go/bin/gosu-amd64 \
	&& /go/bin/gosu-amd64 --version \
	&& /go/bin/gosu-amd64 nobody id \
	&& /go/bin/gosu-amd64 nobody ls -l /proc/self/fd
RUN set -x \
	&& eval "GOARCH=386 go build $BUILD_FLAGS -o /go/bin/gosu-i386" \
	&& file /go/bin/gosu-i386 \
	&& /go/bin/gosu-i386 --version \
	&& /go/bin/gosu-i386 nobody id \
	&& /go/bin/gosu-i386 nobody ls -l /proc/self/fd
RUN set -x \
	&& eval "GOARCH=arm GOARM=5 go build $BUILD_FLAGS -o /go/bin/gosu-armel" \
	&& file /go/bin/gosu-armel
RUN set -x \
	&& eval "GOARCH=arm GOARM=6 go build $BUILD_FLAGS -o /go/bin/gosu-armhf" \
	&& file /go/bin/gosu-armhf
# boo Raspberry Pi, making life hard
#RUN set -x \
#	&& eval "GOARCH=arm GOARM=7 go build $BUILD_FLAGS -o /go/bin/gosu-armhf" \
#	&& file /go/bin/gosu-armhf
RUN set -x \
	&& eval "GOARCH=arm64 go build $BUILD_FLAGS -o /go/bin/gosu-arm64" \
	&& file /go/bin/gosu-arm64
RUN set -x \
	&& eval "GOARCH=ppc64 go build $BUILD_FLAGS -o /go/bin/gosu-ppc64" \
	&& file /go/bin/gosu-ppc64
RUN set -x \
	&& eval "GOARCH=ppc64le go build $BUILD_FLAGS -o /go/bin/gosu-ppc64el" \
	&& file /go/bin/gosu-ppc64el
RUN set -x \
	&& eval "GOARCH=s390x go build $BUILD_FLAGS -o /go/bin/gosu-s390x" \
	&& file /go/bin/gosu-s390x

RUN file /go/bin/gosu-*



RUN mkdir -p "$LIFERAY_HOME" \
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




