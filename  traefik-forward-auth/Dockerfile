FROM golang:1.13-alpine as builder

# Setup
RUN mkdir -p /go/src/github.com/thomseddon/traefik-forward-auth
WORKDIR /go/src/github.com/thomseddon/traefik-forward-auth

# Add libraries
RUN apk add --no-cache git; \
    git clone https://github.com/thomseddon/traefik-forward-auth .

# Copy & build
ADD . /go/src/github.com/thomseddon/traefik-forward-auth/
#RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 GO111MODULE=on go build -a -installsuffix nocgo -o /traefik-forward-auth github.com/thomseddon/traefik-forward-auth/cmd
RUN set -ex; \
	\
 	apkArch="$(apk --print-arch)"; \
	case "$apkArch" in \
        # special flags for go 
        # https://gist.github.com/asukakenji/f15ba7e588ac42795f421b48b8aede63
		# armv[567])  Arch="$apkArch" ;; \
        armv[567])  Arch="arm" ;; \
        aarch64)    Arch='arm64'    ;; \
		# armhf)      Arch='armv6'    ;; \
        armhf)      Arch='arm'    ;; \
		ppc64le)    Arch='ppc64le'  ;; \
		s390x)      Arch='s390x'    ;; \
		x86)        Arch='386'      ;; \
		x86_64)     Arch='amd64'    ;; \
 		*) echo >&2 "error: unsupported architecture: $apkArch"; exit 1 ;; \
 	esac; \
    \
    CGO_ENABLED=0 GOOS=linux GOARCH=${Arch} GO111MODULE=on go build -a -installsuffix nocgo -o /traefik-forward-auth github.com/thomseddon/traefik-forward-auth/cmd


# Copy into scratch container
FROM scratch
COPY --from=builder /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/
COPY --from=builder /traefik-forward-auth ./
ENTRYPOINT ["./traefik-forward-auth"]