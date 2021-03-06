FROM eu.gcr.io/kyma-project/external/golang:1.16.0-alpine as builder

ENV BASE_APP_DIR /workspace/go/src/github.com/kyma-project/kyma/components/iam-kubeconfig-service
WORKDIR ${BASE_APP_DIR}

#
# Copy files
#
COPY go.mod ${BASE_APP_DIR}
COPY go.sum ${BASE_APP_DIR}
COPY ./vendor/ ${BASE_APP_DIR}/vendor/
COPY ./pkg/ ${BASE_APP_DIR}/pkg/
COPY ./internal/ ${BASE_APP_DIR}/internal/
COPY ./cmd/ ${BASE_APP_DIR}/cmd/
COPY ./licenses/ ${BASE_APP_DIR}/licenses/

#
# Build app
#

RUN CGO_ENABLED=0 GOOS=linux go build -v -o app ${BASE_APP_DIR}/cmd/generator/
RUN mkdir /app && mv ./app /app/app && mv ${BASE_APP_DIR}/licenses /app/licenses

FROM eu.gcr.io/kyma-project/external/alpine:3.13.2
LABEL source = git@github.com:kyma-project/kyma.git
WORKDIR /app

#
# Install certificates
#

RUN apk update && apk add ca-certificates && rm -rf /var/cache/apk/*

#
# Copy binary
#

COPY --from=builder /app /app

#
# Run app
#

ENTRYPOINT ["/app/app"]
