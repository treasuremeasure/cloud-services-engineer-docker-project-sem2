FROM golang:latest AS build

RUN groupadd -r appuser && useradd -r -g appuser appuser

WORKDIR /app

COPY go.mod go.sum ./

RUN go mod download

COPY . /app

RUN chown -R appuser:appuser /app

RUN go build -o /app/myapp ./cmd/api



FROM alpine:latest

WORKDIR /app

COPY --from=build /app/myapp .

USER appuser

EXPOSE 8080

CMD ["./myapp"]
