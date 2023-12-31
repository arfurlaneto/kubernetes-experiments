FROM golang:1.15
ARG APP_VERSION
ENV APP_VERSION ${APP_VERSION}
COPY . .
RUN go build -o server .
CMD ["./server"]