# Copyright (c) 2019 Red Hat, Inc.
# This program and the accompanying materials are made
# available under the terms of the Eclipse Public License 2.0
# which is available at https://www.eclipse.org/legal/epl-2.0/
#
# SPDX-License-Identifier: EPL-2.0
#
# Contributors:
#   Red Hat, Inc. - initial API and implementation

# This Dockerfile and folder contents are manually synced from
# https://github.com/che-incubator/che-editor-intellij-community.git

# https://access.redhat.com/containers/?tab=tags#/registry.access.redhat.com/ubi8-minimal
FROM ubi8-minimal:8.2-349

ENV \
    FLUXBOX_VERSION="1.3.7-11" \
    IMLIB2_VERSION="1.4.9-8" \
    PYXDG_VERSION="0.25-16" \
    ALECZAPKA_FONTS_VERSION="1.3-25" \
    HOME=/home/user

COPY . /tmp/assets/


#RUN microdnf install wget java-11-openjdk-devel which # tigervnc-server novnc
#RUN microdnf install -y yum && cd /tmp/assets && yum localinstall *.rpm # fluxbox supervisor
RUN microdnf install -y yum
RUN yum install -y tigervnc-server supervisor wget java-11-openjdk-devel novnc fluxbox which

RUN mkdir /ideaIC-2020.2.2 && tar -zxvf /tmp/assets/ideaIC-2020.2.2.tar.gz --strip-components=1 -C /ideaIC-2020.2.2 && \
    mkdir -p /JetBrains/IdeaIC && \
    mkdir /etc/default/jetbrains && \
    for f in "/JetBrains" "/ideaIC-2020.2.2" "/etc/passwd" "/etc/default/jetbrains"; do \
      echo "Changing permissions on ${f}" && chgrp -R 0 ${f} && \
      chmod -R g+rwX ${f}; \
    done

COPY --chown=0:0 etc/entrypoint.sh /entrypoint.sh
COPY --chown=0:0 etc/prevent-idle-timeout.sh /
COPY --chown=0:0 etc/preliminary-configuration.sh /
COPY --chown=0:0 etc/default/*.xml /etc/default/jetbrains/
COPY --chown=0:0 etc/supervisord.conf /etc/supervisord.conf
# disable toolbar + use another theme
COPY --chown=0:0 etc/fluxbox /home/user/.fluxbox/init
# no security/ custom geometry
COPY etc/tigervnc-config /etc/tigervnc/vncserver-config-mandatory
# Set permissions on /etc/passwd and /home to allow arbitrary users to write
COPY idea.properties /JetBrains/idea.properties
RUN mkdir -p /home/user && chgrp -R 0 /home && chmod -R g=u /etc/passwd /etc/group /home && chmod +x /entrypoint.sh && chmod +x /prevent-idle-timeout.sh && chmod +x /preliminary-configuration.sh
USER 10001
ENV HOME=/home/user
ENV IDEA_PROPERTIES=/JetBrains/idea.properties
WORKDIR /projects
ENTRYPOINT [ "/entrypoint.sh" ]
CMD ["tail", "-f", "/dev/null"]

ENV SUMMARY="Red Hat CodeReady Workspaces - IntelliJ Plugin" \
    DESCRIPTION="Red Hat CodeReady Workspaces - IntelliJ Plugin" \
    PRODNAME="codeready-workspaces" \
    COMPNAME="plugin-intellij-rhel8"

LABEL summary="$SUMMARY" \
      description="$DESCRIPTION" \
      io.k8s.description="$DESCRIPTION" \
      io.k8s.display-name="$DESCRIPTION" \
      io.openshift.tags="$PRODNAME,$COMPNAME" \
      com.redhat.component="$PRODNAME-$COMPNAME-container" \
      name="$PRODNAME/$COMPNAME" \
      version="2.5" \
      license="EPLv2" \
      maintainer="Nick Boldt <nboldt@redhat.com>" \
      io.openshift.expose-services="" \
      usage=""
