# ── Stage 1: Build ────────────────────────────────────────────────
FROM debian:bookworm-slim AS builder
 
ARG PKG_SOURCE_VERSION=master

RUN apt-get update && apt-get install -y --no-install-recommends \
        git \
        ca-certificates \
        gcc \
        g++ \
        make \
        pkg-config \
        libnetfilter-queue-dev \
        libnfnetlink-dev \
        libmnl-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /build

RUN git clone https://github.com/MikeWang000000/FakeSIP.git . \
    && git checkout "${PKG_SOURCE_VERSION}"

RUN make

# ── Stage 2: Runtime ──────────────────────────────────────────────
FROM debian:bookworm-slim

RUN apt-get update && apt-get install -y --no-install-recommends \
        libmnl0 \
        libnetfilter-queue1 \
        libnfnetlink0 \
        iptables \
        nftables \
    && rm -rf /var/lib/apt/lists/*

COPY --from=builder /build/fakesip /usr/local/bin/fakesip

ENTRYPOINT ["fakesip"]
CMD ["--help"]
