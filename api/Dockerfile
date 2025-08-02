FROM buildpack-deps:buster AS isolate

RUN apt-get update && \
    apt-get install -y --no-install-recommends git libcap-dev && \
    rm -rf /var/lib/apt/lists/* && \
    git clone https://github.com/envicutor/isolate.git /tmp/isolate/ && \
    cd /tmp/isolate && \
    git checkout af6db68042c3aa0ded80787fbb78bc0846ea2114 && \
    make -j$(nproc) install && \
    rm -rf /tmp/*

FROM node:15.10.0-buster-slim

ENV DEBIAN_FRONTEND=noninteractive

# Optional: fixes some Render base image issues
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

# Prevent errors from missing locales
RUN sed -i '/en_US.UTF-8/s/^# //g' /etc/locale.gen && \
    apt-get update && \
    apt-get install -y locales && \
    locale-gen

RUN dpkg-reconfigure -p critical dash

# FIXED: removed libgmp3-dev (use libgmp-dev), removed libncurses5
RUN apt-get update && \
    apt-get install -y \
    libxml2 gnupg tar coreutils util-linux libc6-dev \
    binutils build-essential libpcre3-dev libevent-dev libgmp-dev \
    libncurses6 libedit-dev libseccomp-dev rename procps python3 \
    libreadline-dev libblas-dev liblapack-dev libarpack2-dev \
    libfftw3-dev libglpk-dev libqhull-dev libqrupdate-dev \
    libsuitesparse-dev libsundials-dev libpcre2-dev libcap-dev && \
    rm -rf /var/lib/apt/lists/*

RUN useradd -M piston

COPY --from=isolate /usr/local/bin/isolate /usr/local/bin
COPY --from=isolate /usr/local/etc/isolate /usr/local/etc/isolate

WORKDIR /piston_api

COPY ["package.json", "package-lock.json", "./"]
RUN npm install

COPY api/src /piston_api/src

CMD ["/piston_api/src/docker-entrypoint.sh"]
EXPOSE 2000
