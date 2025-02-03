FROM debian:bookworm-slim
ARG TARGETARCH
ARG TARGETVARIANT

# Install wyoming-whisper-cpp
WORKDIR /usr/src
ENV PIP_BREAK_SYSTEM_PACKAGES=1

RUN \
    apt-get update \
    && apt-get install -y --no-install-recommends \
    python3 \
    python3-pip \
    build-essential \
    wget \
    curl \
    ca-certificates \
    python3-numpy \
    python3-scipy

# Copy local wyoming-whisper-cpp code
COPY --from=wyoming-whisper-cpp . /usr/share/wyoming-whisper-cpp

RUN cd /usr/share/wyoming-whisper-cpp/whisper.cpp && \
    make clean && \
    make main \
    && cd /usr/src \
    && pip3 install --no-cache-dir -U \
    setuptools \
    wheel \
    numpy \
    scipy \
    && pip3 install --no-cache-dir \
    --extra-index-url https://www.piwheels.org/simple \
    -f . \
    "/usr/share/wyoming-whisper-cpp" \
    && apt-get purge -y --auto-remove \
    build-essential \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /
COPY run.sh ./

EXPOSE 10300

ENTRYPOINT ["bash", "/run.sh"]

