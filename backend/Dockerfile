FROM golang:latest AS build

WORKDIR /app

RUN groupadd -r appuser && useradd -r -g appuser appuser

RUN chown -R appuser:appuser /app

COPY go.mod go.sum ./

RUN go mod download

COPY . /app

RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 \
    go build -o /app/myapp ./cmd/api


FROM alpine:latest

RUN addgroup -S appuser && adduser -S -G appuser appuser

WORKDIR /app

COPY --from=build /app/myapp .

USER appuser

EXPOSE 8080

CMD ["./myapp"]
