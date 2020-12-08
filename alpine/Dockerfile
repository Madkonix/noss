FROM openjdk:15-jdk-alpine AS build

# Prepare jlinked runtime from openjdk:15-jdk-alpine
RUN ["jlink", "--compress=2", \
     "--module-path", "/opt/java/openjdk/jmods", \
     "--add-modules", "java.base,java.compiler,java.datatransfer,java.desktop,java.instrument,java.logging,java.management,java.management.rmi,java.naming,java.net.http,java.prefs,java.rmi,java.scripting,java.se,java.security.jgss,java.security.sasl,java.smartcardio,java.sql,java.sql.rowset,java.transaction.xa,java.xml,java.xml.crypto,jdk.accessibility,jdk.attach,jdk.charsets,jdk.compiler,jdk.crypto.cryptoki,jdk.crypto.ec,jdk.dynalink,jdk.editpad,jdk.hotspot.agent,jdk.httpserver,jdk.incubator.foreign,jdk.incubator.jpackage,jdk.jartool,jdk.javadoc,jdk.jcmd,jdk.jconsole,jdk.jdeps,jdk.jdi,jdk.jdwp.agent,jdk.jfr,jdk.jlink,jdk.jshell,jdk.jsobject,jdk.jstatd,jdk.localedata,jdk.management,jdk.management.agent,jdk.management.jfr,jdk.naming.dns,jdk.naming.rmi,jdk.net,jdk.nio.mapmode,jdk.sctp,jdk.security.auth,jdk.security.jgss,jdk.unsupported,jdk.xml.dom,jdk.zipfs", \
     "--output", "/tmp/jlink"]

# Get a clean alpine:3.12.1
FROM alpine:3.12.1

# Copy jlinked runtime from previous build to clean alpine:3.12.1
COPY --from=build /tmp/jlink /opt/jdk/

# Install glibc 2.32-r0, gcc-libs 9.2.0-4 and other dependencies
RUN apk --update add --no-cache --virtual=.build-dependencies ca-certificates curl openssl binutils xz \
    && GLIBC_VER="2.32-r0" \
    && ALPINE_GLIBC_REPO="https://github.com/sgerrand/alpine-pkg-glibc/releases/download" \
    && GCC_LIBS_URL="https://archive.archlinux.org/packages/g/gcc-libs/gcc-libs-9.2.0-4-x86_64.pkg.tar.xz" \
    && ZLIB_URL="https://archive.archlinux.org/packages/z/zlib/zlib-1%3A1.2.11-4-x86_64.pkg.tar.xz" \
    && curl -Ls https://alpine-pkgs.sgerrand.com/sgerrand.rsa.pub -o /etc/apk/keys/sgerrand.rsa.pub \
    && curl -Ls ${ALPINE_GLIBC_REPO}/${GLIBC_VER}/glibc-${GLIBC_VER}.apk > /tmp/${GLIBC_VER}.apk \
    && apk add /tmp/${GLIBC_VER}.apk \
    && curl -Ls ${GCC_LIBS_URL} -o /tmp/gcc-libs.tar.xz \
    && mkdir /tmp/gcc \
    && tar -xvf /tmp/gcc-libs.tar.xz -C /tmp/gcc \
    && mv /tmp/gcc/usr/lib/libgcc* /tmp/gcc/usr/lib/libstdc++* /usr/glibc-compat/lib \
    && strip /usr/glibc-compat/lib/libgcc_s.so.* /usr/glibc-compat/lib/libstdc++.so* \
    && curl -Ls ${ZLIB_URL} -o /tmp/libz.tar.xz \
    && mkdir /tmp/libz \
    && tar -xf /tmp/libz.tar.xz -C /tmp/libz \
    && mv /tmp/libz/usr/lib/libz.so* /usr/glibc-compat/lib \
    && apk del binutils \
    && rm -rf /tmp/${GLIBC_VER}.apk /tmp/gcc /tmp/gcc-libs.tar.xz /tmp/libz /tmp/libz.tar.xz /var/cache/apk/*

# Install scala 2.13.4
RUN apk --update add --no-cache --virtual=.build-dependencies ca-certificates \
    && export SCALA_VERSION=2.13.4 \
    && export SCALA_HOME=/usr/share/scala \
    && cd "/tmp" \
    && wget --no-verbose "https://downloads.typesafe.com/scala/${SCALA_VERSION}/scala-${SCALA_VERSION}.tgz" \
    && tar xzf "scala-${SCALA_VERSION}.tgz" \
    && mkdir "${SCALA_HOME}" \
    && rm "/tmp/scala-${SCALA_VERSION}/bin/"*.bat \
    && mv "/tmp/scala-${SCALA_VERSION}/bin" "/tmp/scala-${SCALA_VERSION}/lib" "${SCALA_HOME}" \
    && ln -s "${SCALA_HOME}/bin/"* "/usr/bin/" \
    && apk del .build-dependencies \
    && rm -rf "/tmp/"*

# Install sbt 1.4.4
RUN mkdir -p "/usr/local/sbt" \
    && wget -qO - --no-check-certificate "https://github.com/sbt/sbt/releases/download/v1.4.4/sbt-1.4.4.tgz" | tar xz -C /usr/local/sbt --strip-components=1

# Add jdk and sbt to PATH
ENV PATH="/opt/jdk/bin:/usr/local/sbt/bin:${PATH}"

# Add nodejs, npm, yarn and other dependencies
RUN apk --update add --no-cache git curl nodejs npm bash \
    && npm install -g yarn

# Default workdir to /root/
WORKDIR /root/
