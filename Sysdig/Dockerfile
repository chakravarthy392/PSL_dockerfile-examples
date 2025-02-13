# © Copyright IBM Corporation 2017, 2022.
# LICENSE: Apache License, Version 2.0 (http://www.apache.org/licenses/LICENSE-2.0)

########## Dockerfile for Sysdig version 0.29.3 #########
#
# This Dockerfile builds a basic installation of Sysdig.
#
# Sysdig is open source, system-level exploration: capture system state and activity from a running Linux instance, then save, filter and analyze.
# Sysdig is scriptable in Lua and includes a command line interface and a powerful interactive UI, csysdig, that runs in your terminal.
#
# This image should be built on Ubuntu (18.04, 20.04, 21.10).
#
# To build this image, from the directory containing this Dockerfile
# (assuming that the file is named Dockerfile):
# docker build -t <image_name> .
#
# To guarantee a smooth deployment, the kernel headers must be installed in the host operating system, before running sysdig.
#
# To run sysdig in a Docker container use below command
#  docker run -it --privileged -v /var/run/docker.sock:/host/var/run/docker.sock -v /dev:/dev -v /proc:/host/proc:ro -v
#   /boot:/host/boot:ro -v /lib/modules:/host/lib/modules:rw -v /usr:/host/usr:ro --name <container_name> <image_name>
#
# The official website
# https://www.sysdig.org/
##################################################################################

# Base Image
FROM s390x/ubuntu:18.04

ARG SYSDIG_VER=0.29.3

LABEL maintainer="LoZ Open Source Ecosystem (https://www.ibm.com/community/z/usergroups/opensource)"
LABEL RUN="docker run -i -t --privileged -v /var/run/docker.sock:/host/var/run/docker.sock -v /dev:/dev -v /proc:/host/proc:ro -v /boot:/host/boot:ro -v /lib/modules:/host/lib/modules:rw -v /usr:/host/usr:ro --name NAME IMAGE"

ENV SYSDIG_HOST_ROOT /host

ENV HOME /root

RUN cp /etc/skel/.bashrc /root && cp /etc/skel/.profile /root

ENV SOURCE_ROOT=/tmp/source

WORKDIR $SOURCE_ROOT

# Install dependencies
RUN apt-get update \
        && apt-get install -y git cmake build-essential pkg-config autoconf \
        wget curl patch libtool libelf-dev linux-headers-$(uname -r) kmod dkms \
        libz-dev libssl-dev libcurl4-gnutls-dev libexpat1-dev gettext gcc \
        libgrpc++-dev protobuf-compiler-grpc libprotobuf-dev
        
# Download source code
RUN cd $SOURCE_ROOT \
        && git clone https://github.com/draios/sysdig.git \
        && cd sysdig      \
        && git checkout ${SYSDIG_VER}  \
        && mkdir build \
# Configure Sysdig
        && cd build \
        && cmake -DUSE_BUNDLED_PROTOBUF=Off -DUSE_BUNDLED_GRPC=Off \
                -DSYSDIG_VERSION=${SYSDIG_VER} .. \
# Build and Install Sysdig
        && make \
        && make install \
# Copy important content from build
        && cp $SOURCE_ROOT/sysdig/build/driver/scap.ko $HOME \
        && cp -r /usr/local/bin/sysdig* /usr/bin/ \
        && cp -r /usr/local/bin/csysdig /usr/bin/ \
        && cp -r /usr/local/bin/scap-driver-loader /usr/bin/ \
        && mkdir -p /usr/src/sysdig-${SYSDIG_VER} \
        && cp $SOURCE_ROOT/sysdig/build/driver/src/* /usr/src/sysdig-${SYSDIG_VER}/
# Clean up cache data and remove dependencies which are not required
RUN rm -rf $SOURCE_ROOT \
        && apt-get clean \
        && rm -rf /var/lib/apt/lists/*

# Some base images have an empty /lib/modules by default
# If it's not empty, docker build will fail instead of
# silently overwriting the existing directory
RUN rm -rf /lib/modules \
 && ln -s $SYSDIG_HOST_ROOT/lib/modules /lib/modules

COPY ./docker-entrypoint.sh /
RUN chmod +x /docker-entrypoint.sh

ENTRYPOINT ["/docker-entrypoint.sh"]

CMD ["bash"]
# End of Dockerfile
