# Stage 1: Build and install isolate
FROM buildpack-deps:buster AS isolate

RUN apt-get update && \
    apt-get install -y --no-install-recommends git libcap-dev && \
    rm -rf /var/lib/apt/lists/* && \
    git clone https://github.com/envicutor/isolate.git /tmp/isolate/ && \
    cd /tmp/isolate && \
    git checkout af6db68042c3aa0ded80787fbb78bc0846ea2114 && \
    make -j$(nproc) install && \
    rm -rf /tmp/*

# Stage 2: Final image
FROM node:15.10.0-buster-slim

ENV DEBIAN_FRONTEND=noninteractive
ENV LANG=en_US.UTF-8
ENV LANGUAGE=en_US:en
ENV LC_ALL=en_US.UTF-8

# Install system dependencies and configure locale
RUN apt-get update && \
    apt-get install -y --no-install-recommends \
    locales libxml2 gnupg tar coreutils util-linux libc6-dev \
    binutils build-essential libpcre3-dev libevent-dev libgmp-dev \
    libncurses6 libedit-dev libseccomp-dev rename procps python3 \
    libreadline-dev libblas-dev liblapack-dev libarpack2-dev \
    libfftw3-dev libglpk-dev libqhull-dev libqrupdate-dev \
    libsuitesparse-dev libsundials-dev libpcre2-dev libcap-dev && \
    echo "en_US.UTF-8 UTF-8" > /etc/locale.gen && \
    locale-gen && \
    update-locale LANG=en_US.UTF-8 && \
    rm -rf /var/lib/apt/lists/*

RUN dpkg-reconfigure -p critical dash
RUN useradd -M piston

# Copy isolate binary and config from the first stage
COPY --from=isolate /usr/local/bin/isolate /usr/local/bin
COPY --from=isolate /usr/local/etc/isolate /usr/local/etc/isolate

WORKDIR /piston_api

COPY ["package.json", "package-lock.json", "./"]
RUN npm install

COPY api/src /piston_api/src

CMD ["/piston_api/src/docker-entrypoint.sh"]
EXPOSE 2000
