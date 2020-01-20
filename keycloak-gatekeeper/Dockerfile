FROM golang:1.13-buster as build

ARG GIT_REF=master

WORKDIR /usr/src

RUN curl -L https://github.com/keycloak/keycloak-gatekeeper/archive/$GIT_REF.tar.gz | tar xz \
		&& mv keycloak-gatekeeper-$GIT_REF keycloak-gatekeeper \
		&& cd keycloak-gatekeeper \
		&& make static \
		&& make test

FROM scratch

COPY --from=build /usr/src/keycloak-gatekeeper/bin/keycloak-gatekeeper ./keycloak-gatekeeper

ENTRYPOINT ["/keycloak-gatekeeper"]
