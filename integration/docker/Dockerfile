#
# The Alluxio Open Foundation licenses this work under the Apache License, version 2.0
# (the "License"). You may not use this work except in compliance with the License, which is
# available at www.apache.org/licenses/LICENSE-2.0
#
# This software is distributed on an "AS IS" basis, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND,
# either express or implied, as more fully set forth in the License.
#
# See the NOTICE file distributed with this work for information regarding copyright ownership.
#

# The official `openjdk` image has been retired from Docker Hub, so we use the
# maintained Eclipse Temurin JDK 8 (Alpine) image instead.
FROM eclipse-temurin:8-jdk-alpine

ARG ALLUXIO_TARBALL=http://downloads.alluxio.org/downloads/files/1.8.1/alluxio-1.8.1-bin.tar.gz

RUN apk add --update bash && \
    rm -rf /var/cache/apk/*

# Alluxio 1.8.1's UnixMountInfo.parseMountInfo crashes with a NullPointerException
# on raw /proc/mounts-format `mount` output (no " on "/"(opts)" markup), which
# busybox `mount` can emit inside containers, killing the worker via
# StorageTier.checkEnoughMemSpace(). Provide a `mount` wrapper that emits the
# expected "dev on mountpoint type fstype (opts)" format. /usr/local/bin precedes
# /bin in PATH, so this shadows busybox's mount for the Alluxio JVM.
COPY mount-parser /usr/local/bin/mount
RUN chmod +x /usr/local/bin/mount

ADD ${ALLUXIO_TARBALL} /opt/

# if the tarball was remote, it needs to be untarred
# use ln -s instead of mv to avoid issues with Centos (see https://github.com/moby/moby/issues/27358)
RUN cd /opt && \
    (if ls | grep -q ".tar.gz"; then tar -xzf *.tar.gz && rm *.tar.gz; fi) && \
    ln -s alluxio-* alluxio

COPY conf /opt/alluxio/conf/
COPY entrypoint.sh /

ENTRYPOINT ["/entrypoint.sh"]
