FROM tomcat:7.0-jre8-alpine

ARG MOLGENIS_VERSION

# --no-cache allows users to install packages with an index that is updated and used on-the-fly and not cached locally
# This avoids the need to use --update and remove /var/cache/apk/* when done installing packages.
RUN apk --no-cache add openssl python3 socat

COPY molgenis-app-$MOLGENIS_VERSION.war /

RUN rm -r $CATALINA_HOME/webapps/ROOT \
    && rm -r $CATALINA_HOME/webapps/docs \
    && rm -r $CATALINA_HOME/webapps/examples \
    && mv /molgenis-app-$MOLGENIS_VERSION.war $CATALINA_HOME/webapps/ROOT.war \
	&& echo 'CATALINA_OPTS="-Xmx2g -XX:+UseConcMarkSweepGC -XX:+CMSClassUnloadingEnabled -Dmolgenis.home=/opt/molgenis/ -Dopencpu.uri.port=8004"' > $CATALINA_HOME/bin/setenv.sh

RUN mkdir -p /opt/molgenis \
	&& if ! test -f /opt/molgenis/molgenis-server.properties; then echo -e "db_user=molgenis\ndb_password=molgenis\ndb_uri=jdbc\:postgresql\://db/molgenis\nadmin.password=admin\nelasticsearch.transport.addresses=elasticsearch:9300\nopencpu.uri.host=opencpu\npython_script_executable=/usr/bin/python3" > /opt/molgenis/molgenis-server.properties; fi

VOLUME /opt/molgenis

#CMD ["catalina.sh", "run"]

ARG version=8.3.1
ARG edition=free

ENV GRAPHDB_PARENT_DIR=/opt/graphdb
ENV GRAPHDB_HOME=${GRAPHDB_PARENT_DIR}/home

ENV GRAPHDB_INSTALL_DIR=${GRAPHDB_PARENT_DIR}/dist

ADD graphdb-free-${version}-dist.zip /tmp

RUN mkdir -p ${GRAPHDB_PARENT_DIR} && \
    cd ${GRAPHDB_PARENT_DIR} && \
    unzip /tmp/graphdb-free-${version}-dist.zip && \
    rm /tmp/graphdb-free-${version}-dist.zip && \
    mv graphdb-${edition}-${version} dist && \
    mkdir -p ${GRAPHDB_HOME}

ENV PATH=${GRAPHDB_INSTALL_DIR}/bin:$PATH

#CMD ["-Dgraphdb.home=/opt/graphdb/home"]

#ENTRYPOINT ["/opt/graphdb/dist/bin/graphdb"]

ENTRYPOINT ["sh", "-c", "$CATALINA_HOME/bin/startup.sh & /opt/graphdb/dist/bin/graphdb -Dgraphdb.home=/opt/graphdb/home"]

EXPOSE 7200
