FROM golang:latest AS build

WORKDIR /app

COPY go.mod go.sum ./

RUN go mod download

COPY . /app

RUN go build -o /app/myapp ./cmd/api


FROM alpine:latest

WORKDIR /app

COPY --from=build /app/myapp .

EXPOSE 8080

CMD ["./myapp"]
