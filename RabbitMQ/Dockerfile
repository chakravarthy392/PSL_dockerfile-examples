# © Copyright IBM Corporation 2022.
# LICENSE: Apache License, Version 2.0 (http://www.apache.org/licenses/LICENSE-2.0)
#
# This dockerfile is no longer supported. Please find latest information from https://www.ibm.com/community/z/open-source-software/
#
################# Dockerfile for RabbitMQ version 3.9.16 ################################
#
# RabbitMQ is an open source message broker software (sometimes called message-oriented middleware)
# that implements the Advanced Message Queuing Protocol (AMQP).
#
# To build RabbitMQ image from the directory containing this Dockerfile
# (assuming that the file is named "Dockerfile"):
# docker build -t <image_name> .
#
# To start the RabbitMQ server run the below command
# docker run --name <container_name> -p 15672:15672 -d <image_name>
#
# To start the RabbitMQ server by providing rabbitmq-configuration
# docker run --name <container_name> -v <path_to_rabbitmq.config_file_on_host>:/etc/rabbitmq/rabbitmq.config -p 15672:15672 -d <image_name>
#
# We can view the RabbitMQ management UI at http://<rabbitmq-host-IP>:15672
#
#########################################################################################

# Base image
FROM s390x/ubuntu:18.04

ARG RABBITMQ_VER=3.9.16
ARG ERLANG_VERSION=24.3.3
ARG ELIXIR_VERSION=1.13.4
# The author
LABEL maintainer="LoZ Open Source Ecosystem (https://www.ibm.com/developerworks/community/groups/community/lozopensource)"

ENV SOURCE_DIR=/tmp/source

ENV LC_ALL=en_US.UTF-8
# Install the build dependencies for RabbitMQ
RUN apt-get update && apt-get install -y \
    locales \
	openssl \
	wget \
	tar \
	xz-utils \
	make \
	python3 \
	xsltproc \
	rsync \
	git \
	zip \
	sed \
	wget \
	tar \
	make \
	perl \
	openssl \
	gcc \
	g++ \
	libncurses-dev \
	libncurses5-dev \
	unixodbc \
	unixodbc-dev \
	libssl-dev \
	openjdk-8-jdk \
	libwxgtk3.0-dev \
	xsltproc \
	libxml2-utils \
 &&	ln -sf /usr/bin/python3 /usr/bin/python \
# Install the build dependencies for Erlang
 && apt-get install -y \
    curl \
	autoconf \
	fop \
	flex \
	gawk \
	gcc \
	g++ \
	gzip \
	libncurses-dev \
	libssl-dev \
	libxml2-utils \
	make \
	tar \
	unixodbc-dev \
	wget \
	xsltproc \
# Download and build Erlang
 && wget https://github.com/erlang/otp/releases/download/OTP-$ERLANG_VERSION/otp_src_$ERLANG_VERSION.tar.gz \
 && tar zxf otp_src_$ERLANG_VERSION.tar.gz && cd otp_src_$ERLANG_VERSION \
 && export ERL_TOP=$(pwd) && ./configure --prefix=/usr \
 && make && make install \
# Download and install rabbitmq
 && mkdir $SOURCE_DIR && cd $SOURCE_DIR \
 && wget https://github.com/rabbitmq/rabbitmq-server/releases/download/v$RABBITMQ_VER/rabbitmq-server-$RABBITMQ_VER.tar.xz \
 && localedef -c -f UTF-8 -i en_US en_US.UTF-8 \
 && git clone https://github.com/elixir-lang/elixir \
 && cd elixir && git checkout v$ELIXIR_VERSION && make && make install \
 && cd /opt && tar xf $SOURCE_DIR/rabbitmq-server-$RABBITMQ_VER.tar.xz \
 && cd rabbitmq-server-$RABBITMQ_VER  && make && make install \
 && mkdir -p /etc/rabbitmq \
 && echo "[{rabbit, [{loopback_users, []}]}]." | tee /etc/rabbitmq/rabbitmq.config \
 && rm -rf deps/rabbit/plugins && ln -s  $PWD/plugins deps/rabbit/plugins \
# Clean up of unused packages and source directory.
 && rm -rf $SOURCE_DIR \
 && apt-get remove -y \
    ant \
    curl \
    git \
    make \
    rsync \
    wget \
    xsltproc \
    xz-utils \
    zip \
 && apt-get autoremove -y \
 && apt autoremove -y \
 && apt-get clean && rm -rf /var/lib/apt/lists/*

WORKDIR /opt/rabbitmq-server-$RABBITMQ_VER

# Expose RabbitMQ management console port and RabbitMQ server port
EXPOSE 15672 5672

# Enable RabbitMQ management plugin and start RabbitMQ server
ENTRYPOINT sbin/rabbitmq-plugins enable rabbitmq_management && sbin/rabbitmq-server

# End of Dockerfile
