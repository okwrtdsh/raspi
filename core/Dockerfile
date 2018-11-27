FROM alpine:latest AS builder

RUN apk add --no-cache curl \
 && while [ "$QEMU_DOWNLOAD_URL" == "" ] ; do QEMU_DOWNLOAD_URL=$(curl -s https://api.github.com/repos/multiarch/qemu-user-static/releases/latest | grep "browser_download_url" | grep "\/qemu-arm-static.tar.gz" | cut -d\" -f4) ; done \
 && curl --retry 3 -L -s -o /tmp/qemu-arm-static.tar.gz $QEMU_DOWNLOAD_URL \
 && tar xzf /tmp/qemu-arm-static.tar.gz -C /tmp

FROM arm32v7/python:3.7.1-stretch
COPY --from=builder /tmp/qemu-arm-static /usr/bin

ENV DEBIAN_FRONTEND=noninteractive \
	LANG=C.UTF-8 \
	LC_ALL=C.UTF-8
