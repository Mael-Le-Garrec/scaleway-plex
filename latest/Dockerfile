## -*- docker-image-name: "scaleway/plex:latest" -*-
ARG SCW_ARCH
FROM scaleway/ubuntu:${SCW_ARCH}-bionic

MAINTAINER Scaleway <opensource@scaleway.com> (@scaleway)

# Prepare rootfs for image-builder
RUN /usr/local/sbin/builder-enter

# Install packages
RUN echo deb https://downloads.plex.tv/repo/deb public main | tee /etc/apt/sources.list.d/plexmediaserver.list \
  && curl https://downloads.plex.tv/plex-keys/PlexSign.key | apt-key add - \
  && apt-get update \
  && apt-get -y -o Dpkg::Options::="--force-confdef" -o Dpkg::Options::="--force-confold" install \
     plexmediaserver \
     apache2 \
     s3fs \
     pandoc \
     python3-pip \
  && pip3 install awscli awscli-plugin-endpoint \
  && a2enmod proxy_http proxy

# Copy files
COPY overlay ./

# Give permissions to Plex user
RUN chown -R plex:plex /var/lib/plexmediaserver/

# Run plex at startup
RUN systemctl enable plexmediaserver init-plex

# Symlink doc readme to /root/ and create website index tutorial
RUN ln -s /usr/share/doc/scaleway/plex/README /root/README \
    && pandoc /usr/share/doc/scaleway/plex/README -o /var/www/html/index.html

# Clean rootfs from image-builder
RUN /usr/local/sbin/builder-leave
