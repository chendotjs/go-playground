# Copyright 2017 The Go Authors. All rights reserved.
# Use of this source code is governed by a BSD-style
# license that can be found in the LICENSE file.
FROM debian:buster as builder
LABEL maintainer "golang-dev@googlegroups.com"

ENV GOPATH /go
ENV GOCACHE /go/.cache
ENV PATH /usr/local/go/bin:$GOPATH/bin:$PATH
ENV GOROOT_BOOTSTRAP /usr/local/gobootstrap
ENV GO_VERSION 1.20

RUN sed -i "s/http/https/g" /etc/apt/sources.list
RUN apt-get -o "Acquire::https::Verify-Peer=false" update && apt-get -o "Acquire::https::Verify-Peer=false" install -y ca-certificates curl

# Get the Go binary.
RUN curl -sSL https://go.dev/dl/go$GO_VERSION.linux-amd64.tar.gz -o /tmp/go.tar.gz
RUN tar -C /usr/local/ -vxzf /tmp/go.tar.gz

# Add and compile playground daemon
COPY . /go/src/playground/
RUN cd /go/src/playground && go mod init main && go mod tidy && go build -o playground . && mkdir -p /go/bin/ && mv playground /go/bin

FROM debian:buster

RUN sed -i "s/http/https/g" /etc/apt/sources.list
RUN apt-get -o "Acquire::https::Verify-Peer=false" update && apt-get -o "Acquire::https::Verify-Peer=false" install -y ca-certificates git --no-install-recommends

COPY --from=builder /usr/local/go /usr/local/go

ENV GOPATH /go
ENV GOCACHE /tmp/.cache

ENV PATH /usr/local/go/bin:$GOPATH/bin:$PATH

RUN mkdir /app

COPY --from=builder /go/bin/playground /app
WORKDIR /app


EXPOSE 8080
ENTRYPOINT ["/app/playground"]
