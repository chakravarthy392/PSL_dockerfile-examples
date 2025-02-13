# © Copyright IBM Corporation 2022.
# LICENSE: Apache License, Version 2.0 (http://www.apache.org/licenses/LICENSE-2.0)

#################################### Dockerfile for Bazel version 5.1.1 ####################################
#
# To build a Bazel image from the directory containing this Dockerfile:
# (assuming that the file is named Dockerfile):
#   docker buildx build -t <image_name> .
#
# This Dockerfile requires the docker buildx plugin. For information on buildx see:
#   https://docs.docker.com/buildx/working-with-buildx/
#
# For information on running the container see:
#   https://bazel.build/docs/bazel-container
############################################################################################################

FROM s390x/ubuntu:20.04 AS base_image

LABEL maintainer="LoZ Open Source Ecosystem (https://www.ibm.com/community/z/usergroups/opensource)"

RUN --mount=source=install_packages.sh,target=/mnt/install_packages.sh,type=bind \
    /mnt/install_packages.sh

FROM base_image AS builder

WORKDIR /var/bazel
RUN --mount=source=install_bazel.sh,target=/mnt/install_bazel.sh,type=bind \
    /mnt/install_bazel.sh

FROM base_image

LABEL maintainer="LoZ Open Source Ecosystem (https://www.ibm.com/community/z/usergroups/opensource)"

RUN useradd --system --create-home --home-dir=/home/ubuntu --shell=/bin/bash --gid=root --groups=sudo --uid=1000 ubuntu
USER ubuntu
WORKDIR /home/ubuntu
COPY --from=builder /var/bazel/bazel /usr/local/bin/bazel
ENTRYPOINT ["/usr/local/bin/bazel"]
