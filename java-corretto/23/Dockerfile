FROM        amazoncorretto:23

LABEL       author="Doom2615" maintainer="doom@atbphosting.com"
LABEL       org.opencontainers.image.source="https://github.com/Doom2615/atbp-images"
LABEL       org.opencontainers.image.licenses="MIT"

# Added --allowerasing to resolve the curl-minimal vs curl conflict
RUN         dnf upgrade -y --releasever=latest --allowerasing \
            && dnf install -y --releasever=latest --allowerasing \
            curl \
            ca-certificates \
            openssl \
            git \
            tar \
            sqlite \
            fontconfig \
            freetype \
            libstdc++ \
            freetype-devel \
            lsof \
            tzdata \
            iproute \
            gcc \
            gcc-c++ \
            automake \
            make \
            libtool \
            shadow-utils \
            && dnf clean all \
            && rm -rf /var/cache/dnf \
            && useradd -m -d /home/container container

ENV         LC_ALL=en_US.UTF-8
ENV         LANG=en_US.UTF-8
ENV         LANGUAGE=en_US.UTF-8

# CVE-2026-91403: distrust the compromised eMudhra intermediates until the JDK vendor ships its fix
USER        root
COPY        ./emudhra-blocked.certs /tmp/emudhra-blocked.certs
RUN         J="${JAVA_HOME:-$(dirname "$(dirname "$(readlink -f "$(command -v java)")")")}"; F="$J/lib/security/blocked.certs"; [ -f "$F" ] || F="$J/lib/security/blacklisted.certs"; [ -f "$F" ] || F="$J/jre/lib/security/blacklisted.certs"; [ -f "$F" ] || F="$(dirname "$J")/lib/security/blacklisted.certs";             test -f "$F" && echo >> "$F" && cat /tmp/emudhra-blocked.certs >> "$F"             && [ "$(grep -cxF -f /tmp/emudhra-blocked.certs "$F")" -ge 16 ] && rm /tmp/emudhra-blocked.certs

USER        container
ENV         USER=container HOME=/home/container
WORKDIR     /home/container

COPY        ./entrypoint.sh /entrypoint.sh
CMD         [ "/bin/bash", "/entrypoint.sh" ]
