ARG BASE_IMAGE=mcr.microsoft.com/java/jre:8u181-zulu-alpine
FROM $BASE_IMAGE

LABEL maintainer="Azure App Services Container Images <appsvc-images@microsoft.com>"

ENV AI_VERSION 2.1.2
ENV TOMCAT_VERSION 9.0.12
ENV TOMCAT_MAJOR 9

ENV JAVA_OPTS -Djdk.tls.ephemeralDHKeySize=2048 $JAVA_OPTS
ENV JAVA_OPTS -Djava.protocol.handler.pkgs=org.apache.catalina.webresources $JAVA_OPTS
ENV JAVA_OPTS -Djava.util.logging.config.file=/usr/local/tomcat/conf/logging.properties $JAVA_OPTS
ENV JAVA_OPTS -Djava.util.logging.manager=org.apache.juli.ClassLoaderLogManager $JAVA_OPTS
ENV JAVA_OPTS -Dsite.logdir=/home/LogFiles $JAVA_OPTS
ENV JAVA_OPTS -Dsite.home=/home $JAVA_OPTS
ENV JAVA_OPTS -Dsite.tempdir=/tmp $JAVA_OPTS
ENV JAVA_OPTS -Dport.http=80 $JAVA_OPTS
ENV _JAVA_OPTIONS -Djava.net.preferIPv4Stack=true

ENV PATH /usr/local/tomcat/bin:$PATH

# Remove the sample webapps provided by Tomcat
RUN rm -rf /usr/local/tomcat/webapps/

# Remove the artifacts created by the base Java image, as we know we don't need them
RUN rm -rf /tmp/webapps/

COPY init_container.sh /bin/init_container.sh
COPY web.xml /tmp/tomcat/conf/web.xml
#COPY index.jsp /tmp/tomcat/webapps/ROOT/index.jsp
#COPY background.png /tmp/tomcat/webapps/ROOT/background.png
COPY sshd_config /etc/ssh/
#COPY tmp/shared/app_insights/AI-Agent.xml /usr/local/app_insights/aiagent/
#COPY tmp/shared/app_insights/ApplicationInsights.xml /usr/local/app_insights/tomcat_lib/

RUN apk add --update openssh-server bash openrc \
        && rm -rf /var/cache/apk/* \
        && echo "root:Docker!" | chpasswd \
        && wget -O /tmp/apache-tomcat-$TOMCAT_VERSION.tar.gz https://archive.apache.org/dist/tomcat/tomcat-$TOMCAT_MAJOR/v$TOMCAT_VERSION/bin/apache-tomcat-$TOMCAT_VERSION.tar.gz --no-verbose \
        && tar xvzf /tmp/apache-tomcat-$TOMCAT_VERSION.tar.gz -C /tmp \
        && mv /tmp/apache-tomcat-$TOMCAT_VERSION /usr/local/tomcat \
        && rm -rf /usr/local/tomcat/webapps \
        && rm -f /tmp/apache-tomcat-$TOMCAT_VERSION.tar.gz \
#        && wget -O /usr/local/app_insights/tomcat_lib/applicationinsights-core-$AI_VERSION.jar https://github.com/Microsoft/ApplicationInsights-Java/releases/download/$AI_VERSION/applicationinsights-core-$AI_VERSION.jar --no-verbose \
#        && wget -O /usr/local/app_insights/tomcat_lib/applicationinsights-web-$AI_VERSION.jar https://github.com/Microsoft/ApplicationInsights-Java/releases/download/$AI_VERSION/applicationinsights-web-$AI_VERSION.jar --no-verbose \
#        && wget -O /usr/local/app_insights/aiagent/applicationinsights-agent-$AI_VERSION.jar https://github.com/Microsoft/ApplicationInsights-Java/releases/download/$AI_VERSION/applicationinsights-agent-$AI_VERSION.jar --no-verbose \
        && chmod 755 /bin/init_container.sh 

COPY server.xml /usr/local/tomcat/conf/server.xml
COPY catalina.properties /usr/local/tomcat/conf/catalina.properties
COPY context.xml /usr/local/tomcat/conf/context.xml
COPY logging.properties /usr/local/tomcat/conf/logging.properties
COPY spring-boot-web-jsp-1.0.war /tmp/tomcat/webapps/ROOT.war

EXPOSE 80 2222

ENTRYPOINT ["/bin/init_container.sh"]
