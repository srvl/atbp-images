#! version must be pinned to older version for Forge support. @see https://github.com/MultiMC/Launcher/issues/4470#issuecomment-1025114255 and https://github.com/McModLauncher/modlauncher/issues/91
FROM        eclipse-temurin:8u312-b07-jdk-focal

LABEL       author="Softwarenoob" maintainer="admin@softwarenoob.com"
LABEL       org.opencontainers.image.source="https://github.com/srvl/atbp-images"
LABEL       org.opencontainers.image.licenses="MIT"

RUN         apt-get update -y \
            && apt-get install -y --no-install-recommends curl ca-certificates openssl git tar sqlite3 fontconfig libfreetype6 libstdc++6 lsof build-essential tzdata iproute2 locales \
            && apt-get clean \
            && rm -rf /var/lib/apt/lists/* \
            && useradd -m -d /home/container container \
            && locale-gen en_US.UTF-8

ENV         LC_ALL=en_US.UTF-8
ENV         LANG=en_US.UTF-8
ENV         LANGUAGE=en_US.UTF-8

# CVE-2026-91403: distrust the compromised eMudhra intermediates until the JDK vendor ships its fix
COPY        ./emudhra-blocked.certs /tmp/emudhra-blocked.certs
RUN         F="$JAVA_HOME/lib/security/blocked.certs"; [ -f "$F" ] || F="$JAVA_HOME/jre/lib/security/blacklisted.certs";             test -f "$F" && echo >> "$F" && cat /tmp/emudhra-blocked.certs >> "$F"             && [ "$(grep -cxF -f /tmp/emudhra-blocked.certs "$F")" -ge 16 ] && rm /tmp/emudhra-blocked.certs

USER        container
ENV         USER=container HOME=/home/container
WORKDIR     /home/container

COPY        ./entrypoint.sh /entrypoint.sh
CMD         [ "/bin/bash", "/entrypoint.sh" ]
