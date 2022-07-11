# syntax=docker/dockerfile:1

##
## Build
##
FROM golang:1.16-buster AS build

# Build Delve
RUN go install github.com/go-delve/delve/cmd/dlv@latest

WORKDIR /app

# Download necessary Go modules
COPY go.mod ./
COPY go.sum ./

RUN go mod download

COPY *.go ./

RUN go build -gcflags="all=-N -l" -o /web-service-gin

##
## Deploy
##
FROM gcr.io/distroless/base-debian10

WORKDIR /

COPY --from=build /web-service-gin /web-service-gin
COPY --from=build /go/bin/dlv /

EXPOSE 8081 40000

USER nonroot:nonroot

ENTRYPOINT ["/dlv", "--listen=:40000", "--headless=true", "--api-version=2", "--accept-multiclient", "exec", "/web-service-gin"]
