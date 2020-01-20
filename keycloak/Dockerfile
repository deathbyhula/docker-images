FROM debian:buster

ENV KEYCLOAK_VERSION 7.0.1
ENV JDBC_POSTGRES_VERSION 42.2.5
ENV JDBC_MYSQL_VERSION 5.1.46
ENV JDBC_MARIADB_VERSION 2.2.3
ENV JDBC_MSSQL_VERSION 7.4.1.jre8

ENV LAUNCH_JBOSS_IN_BACKGROUND 1
ENV PROXY_ADDRESS_FORWARDING false
ENV JBOSS_HOME /opt/jboss/keycloak
ENV LANG en_US.UTF-8

ARG GIT_REPO
ARG GIT_BRANCH
ARG KEYCLOAK_DIST=https://downloads.jboss.org/keycloak/$KEYCLOAK_VERSION/keycloak-$KEYCLOAK_VERSION.tar.gz

USER root
WORKDIR /opt/jboss

RUN apt-get -qq update \
	&& apt-get install -y --no-install-recommends \
		bsdtar \
    curl \
		unzip \
		openjdk-8-jdk-headless \
		jq \
		openssl \
		ca-certificates \
    ca-certificates-java \
		subversion \
	&& rm -rf /var/lib/apt/lists/* \
  && update-ca-certificates \
	&& svn export https://github.com/keycloak/keycloak-containers.git/tags/$KEYCLOAK_VERSION/server/tools tools \
	&& apt-get remove -y --purge subversion

#ENV JAVA_HOME /usr/lib/jvm/default-java

RUN groupadd -r jboss -g 1000 \
	&& useradd -u 1000 -r -g jboss -m -d /opt/jboss -s /sbin/nologin -c "JBoss user" jboss \
	&& chmod 755 /opt/jboss \
  && export JAVA_HOME=""/usr/lib/openjdk-8-jdk-$(dpkg --print-architecture)""

RUN sh /opt/jboss/tools/build-keycloak.sh

USER jboss

EXPOSE 8080
EXPOSE 8443

ENTRYPOINT ["/opt/jboss/tools/docker-entrypoint.sh"]
CMD ["-b", "0.0.0.0"]

