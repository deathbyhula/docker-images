FROM centos:7

ENV KEYCLOAK_VERSION 7.0.0
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

RUN yum update -y \
	&& yum install -y xmlstarlet saxon augeas bsdtar unzip \
	&& yum install -y java-1.8.0-openjdk-devel \
	&& yum install -y epel-release git \
	&& yum install -y jq openssl which \
	&& yum clean all
RUN groupadd -r jboss -g 1000 \
	&& useradd -u 1000 -r -g jboss -m -d /opt/jboss -s /sbin/nologin -c "JBoss user" jboss \
	&& chmod 755 /opt/jboss
WORKDIR /opt/jboss

ENV JAVA_HOME /usr/lib/jvm/java

ADD tools /opt/jboss/tools
RUN /opt/jboss/tools/build-keycloak.sh

USER jboss

EXPOSE 8080
EXPOSE 8443

ENTRYPOINT [ "/opt/jboss/tools/docker-entrypoint.sh" ]

CMD ["-b", "0.0.0.0"]

