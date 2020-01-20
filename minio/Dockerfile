#
# build image
#

FROM golang:1.13-alpine as build

ARG GIT_REF=master

ENV GOPATH /go
ENV CGO_ENABLED 0
ENV CGO_111MODULE on

RUN apk add --no-cache \
		ca-certificates \
		curl \
		git \
	&& curl -L https://github.com/minio/minio/archive/$GIT_REF.tar.gz | tar xz \
		&& mv minio-$GIT_REF minio \
		&& cd minio \
		&& go install -v -ldflags "$(go run buildscripts/gen-ldflags.go)"

#
# production image
#

FROM alpine:3.10

ENV MINIO_UPDATE off
ENV MINIO_ACCESS_KEY_FILE=access_key \
		MINIO_SECRET_KEY_FILE=secret_key \
		MINIO_KMS_MASTER_KEY_FILE=kms_master_key \
		MINIO_SSE_MASTER_KEY_FILE=sse_master_key

EXPOSE 9000

COPY --from=build /go/bin/minio /usr/bin/minio
COPY ./docker-entrypoint.sh /usr/bin/

RUN apk add --no-cache \
		ca-certificates \
		'curl>7.61.0' \
		'su-exec>=0.2' \
	&& echo 'hosts: files mdns4_minimal [NOTFOUND=return] dns mdns4' >> /etc/nsswitch.conf

ENTRYPOINT ["/usr/bin/docker-entrypoint.sh"]

VOLUME ["/data"]

CMD ["minio"]

