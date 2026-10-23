FROM        azul/zulu-openjdk:17

LABEL       author="Software-noob" maintainer="admin@softwarenoob.com"
LABEL       org.opencontainers.image.source="https://github.com/Software-Noob/pterodactyl-images"
LABEL       org.opencontainers.image.licenses="MIT"

ENV         DEBIAN_FRONTEND noninteractive

RUN         apt-get update \
            && apt-get -y install --no-install-recommends curl ffmpeg iproute2 git sqlite3 python3 tzdata ca-certificates dnsutils fontconfig libfreetype6 libstdc++6 lsof build-essential locales \
            && apt-get clean \
            && rm -rf /var/lib/apt/lists/* \
            && useradd -m -d /home/container container \
            && locale-gen en_US.UTF-8

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
