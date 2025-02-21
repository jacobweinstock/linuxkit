FROM linuxkit/alpine:e9313150d2cf07ad92b0adb86799dedd9a0476e4 AS mirror

RUN mkdir -p /out/etc/apk && cp -r /etc/apk/* /out/etc/apk/
RUN apk add --no-cache --initdb -p /out \
    alpine-baselayout \
    busybox \
    e2fsprogs \
    e2fsprogs-extra \
    btrfs-progs \
    xfsprogs \
    musl \
    sfdisk \
    util-linux \
    blkid \
    && true
RUN rm -rf /out/etc/apk /out/lib/apk /out/var/cache

FROM linuxkit/alpine:e9313150d2cf07ad92b0adb86799dedd9a0476e4 AS build

RUN apk add --no-cache go musl-dev
ENV GOPATH=/go PATH=$PATH:/go/bin
# Hack to work around an issue with go on arm64 requiring gcc
RUN [ $(uname -m) = aarch64 ] && apk add --no-cache gcc || true
ARG GOPKGVERSION
ENV ldflags="-X main.Version=$GOPKGVERSION"

COPY . /go/src/format/
RUN go-compile.sh /go/src/format

FROM scratch
ENTRYPOINT []
CMD []
WORKDIR /
COPY --from=mirror /out/ /
COPY --from=build /go/bin/format usr/bin/format
CMD ["/usr/bin/format"]
