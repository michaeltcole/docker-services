FROM alpine

MAINTAINER Mumie

ENV USER_UID="1002" \
    USER_NAME="jdownloader" \
    JDOWNLOADER_LINK="http://installer.jdownloader.org/JDownloader.jar"
    
ENV JAVA_VERSION=8 \
    JAVA_VERSION_MINOR=131 \
    JAVA_VERSION_BUILD=11 \
    JAVA_PACKAGE=server-jre \
    JAVA_HOME=/opt/jdk \
    PATH=${PATH}:/opt/jdk/bin \
    GLIBC_VERSION=2.27-r0 \
    LANG=C.UTF-8

# alpine - openjdk8-jre-base java-jna openjdk8-jre-lib libstdc++  glibc-bin glibc-i18n
# libstdc++ and glibc-i18n needed for jdownloader building 7zip Package
RUN set -ex \
    && apk add --no-cache --update wget bash ca-certificates su-exec curl libstdc++ \
    && wget -q -O /etc/apk/keys/sgerrand.rsa.pub https://raw.githubusercontent.com/sgerrand/alpine-pkg-glibc/master/sgerrand.rsa.pub \
#    && apk add --no-cache -X https://apkproxy.heroku.com/andyshinn/alpine-pkg-glibc glibc glibc-i18n \
#   apkproxy.heroku.com/andyshinn proxy broken

    && wget https://github.com/sgerrand/alpine-pkg-glibc/releases/download/${GLIBC_VERSION}/glibc-${GLIBC_VERSION}.apk \
    && wget https://github.com/sgerrand/alpine-pkg-glibc/releases/download/${GLIBC_VERSION}/glibc-bin-${GLIBC_VERSION}.apk \
    && wget https://github.com/sgerrand/alpine-pkg-glibc/releases/download/${GLIBC_VERSION}/glibc-i18n-${GLIBC_VERSION}.apk \
    && apk add glibc-${GLIBC_VERSION}.apk glibc-bin-${GLIBC_VERSION}.apk glibc-i18n-${GLIBC_VERSION}.apk \

#    && ( /usr/glibc-compat/bin/localedef --force --inputfile POSIX --charmap UTF-8 C.UTF-8 || true ) \
#    && echo "export $LANG" > /etc/profile.d/locale.sh \
#    && /usr/glibc-compat/sbin/ldconfig /lib /usr/glibc-compat/lib \
    # Downloading Oracle Java Server-Jre
    && mkdir /opt \
    && curl -jksSLH "Cookie: oraclelicense=accept-securebackup-cookie" -o /tmp/java.tar.gz \
        http://download.oracle.com/otn-pub/java/jdk/${JAVA_VERSION}u${JAVA_VERSION_MINOR}-b${JAVA_VERSION_BUILD}/d54c1d3a095b4ff2b6607d096fa80163/${JAVA_PACKAGE}-${JAVA_VERSION}u${JAVA_VERSION_MINOR}-linux-x64.tar.gz \
    && tar -C /opt -zxvf /tmp/java.tar.gz \
    && ln -s /opt/jdk1.${JAVA_VERSION}.0_${JAVA_VERSION_MINOR} /opt/jdk \
    && find /opt/jdk/ -maxdepth 1 -mindepth 1 | grep -v jre | xargs rm -rf \
    && cd /opt/jdk/ \
    && ln -s ./jre/bin ./bin \
    # adding $USER_NAME as Group and User
    && addgroup -g ${USER_UID} ${USER_NAME} \
    && adduser -D -u ${USER_UID} -G ${USER_NAME} -s /bin/sh -h /${USER_NAME} ${USER_NAME} \
    # Downloading jDownloader jar File
    && wget -O /${USER_NAME}/JDownloader.jar --progress=bar:force ${JDOWNLOADER_LINK} \

#    && apk del wget ca-certificates curl glibc-i18n \
    && apk del wget ca-certificates curl \
    && rm -rf /tmp/* /var/cache/apk/* /var/lib/apk/lists/*

ADD start.sh /start.sh
RUN chmod +x /start.sh

#USER $USER_NAME
VOLUME /${USER_NAME}/cfg
WORKDIR /${USER_NAME}
CMD ["/start.sh"]

# Run Commands
#-v /pathtoconfig/jdownloader:/jdownloader/cfg
#-v /downloadpath/:/jdownloader/Downloads