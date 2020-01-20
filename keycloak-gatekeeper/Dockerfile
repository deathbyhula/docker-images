#
# build image
#

FROM golang:1.13-alpine as build

ARG GIT_REF=master


RUN apk update \
	&& apk add --no-cache \
		ca-certificates \
		curl \
		gcc \
		git \
		make \
		musl-dev \
		openssl \
	&& update-ca-certificates

ENV USER=keycloak
ENV UID=10001

RUN adduser \
	--disabled-password \
	--gecos "" \
	--home "/" \
	--shell "/sbin/nologin" \
	--no-create-home \
	--uid "${UID}" \
	"${USER}"

WORKDIR /usr/src

RUN curl -L https://github.com/keycloak/keycloak-gatekeeper/archive/$GIT_REF.tar.gz | tar xz \
		&& mv keycloak-gatekeeper-$GIT_REF keycloak-gatekeeper \
		&& cd keycloak-gatekeeper \
		&& make static \
		&& make test

#
# container image
#

FROM scratch

ENV PROXY_LISTEN=:3000

COPY --from=build /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/
COPY --from=build /etc/passwd /etc/passwd
COPY --from=build /etc/group  /etc/group
COPY --from=build /usr/src/keycloak-gatekeeper/bin/keycloak-gatekeeper ./keycloak-gatekeeper

USER keycloak:keycloak
EXPOSE 3000

ENTRYPOINT ["/keycloak-gatekeeper"]
