FROM        container-registry.oracle.com/graalvm/jdk:21

LABEL       author="Doom2615" maintainer="doom@atbphosting.com"
LABEL       org.opencontainers.image.source="https://github.com/srvl/atbp-images"
LABEL       org.opencontainers.image.licenses="MIT"

RUN         microdnf update \
            && microdnf install -y curl ca-certificates openssl git tar sqlite fontconfig tzdata iproute gcc gcc-c++ freetype libstdc++ lsof glibc-locale-source glibc-langpack-en \
            && microdnf clean all \
            && adduser --home-dir /home/container container

ENV         LC_ALL=en_US.UTF-8
ENV         LANG=en_US.UTF-8
ENV         LANGUAGE=en_US.UTF-8
# CVE-2026-114938 (Java4Shell): the AVX-512 Base64 decode intrinsic can write past its buffer on x86 CPUs with
# AVX-512 VBMI (Zen 4/5, Ice Lake+). Off until the JDK vendor ships a fixed build; the flag exists on every arch.
ENV         JDK_JAVA_OPTIONS=-XX:-UseBASE64Intrinsics

# CVE-2026-91403: distrust the compromised eMudhra intermediates until the JDK vendor ships its fix
COPY        ./emudhra-blocked.certs /tmp/emudhra-blocked.certs
RUN         F="$JAVA_HOME/lib/security/blocked.certs"; [ -f "$F" ] || F="$JAVA_HOME/jre/lib/security/blacklisted.certs";             test -f "$F" && echo >> "$F" && cat /tmp/emudhra-blocked.certs >> "$F"             && [ "$(grep -cxF -f /tmp/emudhra-blocked.certs "$F")" -ge 16 ] && rm /tmp/emudhra-blocked.certs

USER        container
ENV         USER=container HOME=/home/container
WORKDIR     /home/container

COPY        ./entrypoint.sh /entrypoint.sh
CMD         [ "/bin/bash", "/entrypoint.sh" ]
