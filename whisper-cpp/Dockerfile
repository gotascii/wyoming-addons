FROM ubuntu:22.04
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
    gcc-11 \
    wget \
    curl \
    ca-certificates \
    python3-numpy \
    python3-scipy

# Copy local wyoming-whisper-cpp code
COPY --from=wyoming-whisper-cpp . /usr/share/wyoming-whisper-cpp

# Build with appropriate compiler
ENV CC=gcc-11
ENV CXX=g++-11
ENV CFLAGS="-I. -O3 -DNDEBUG -std=c11 -fPIC -D_XOPEN_SOURCE=600 -D_GNU_SOURCE -pthread"
ENV CXXFLAGS="-I. -I./examples -O3 -DNDEBUG -std=c++11 -fPIC -D_XOPEN_SOURCE=600 -D_GNU_SOURCE -pthread"

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
    gcc-11 \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /
COPY run.sh ./

EXPOSE 10300

ENTRYPOINT ["bash", "/run.sh"]
