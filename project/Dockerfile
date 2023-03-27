### For htmltopdf 
FROM surnet/alpine-wkhtmltopdf:3.16.2-0.12.6-full as wkhtmltopdf

FROM golang:alpine

LABEL maintainer="Mr Beyond"



### Install dependencies for wkhtmltopdf
RUN apk add --no-cache \
    libstdc++ \
    libx11 \
    libxrender \
    libxext \
    libssl1.1 \
    ca-certificates \
    fontconfig \
    freetype \
    ttf-dejavu \
    ttf-droid \
    ttf-freefont \
    ttf-liberation \
    # more fonts
  && apk add --no-cache --virtual .build-deps \
    msttcorefonts-installer \
  # Install microsoft fonts
  && update-ms-fonts \
  && fc-cache -f \
  # Clean up when done
  && rm -rf /tmp/* \
  && apk del .build-deps



# Install git.
# Git is required for fetching the dependencies.
RUN apk update && apk add --no-cache git && apk add --no-cach bash && apk add build-base

RUN mkdir /app

WORKDIR /app

COPY . .

COPY .env .

# Copy wkhtmltopdf files from docker-wkhtmltopdf image
COPY --from=wkhtmltopdf /bin/wkhtmltopdf /bin/wkhtmltopdf

RUN go get -d -v ./...

RUN go install -v ./...

# Build the Go app
RUN go build -o /build

# Expose port 8080 to the outside world
EXPOSE 8080

# Run the executable
# CMD [ "/build" ]

RUN go get github.com/githubnemo/CompileDaemon
RUN go install -mod=mod github.com/githubnemo/CompileDaemon

RUN go get -v golang.org/x/tools/gopls

ENTRYPOINT CompileDaemon --build="go build -a -buildvcs=false -installsuffix cgo -o main ." --command="./main"