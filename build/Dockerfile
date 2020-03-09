# --- Build the bridge
FROM centos:8 AS builder

RUN yum install epel-release -y && \
        yum update -y --setopt=tsflags=nodocs && \
        yum install qpid-proton-c-devel --setopt=tsflags=nodocs -y && \
        dnf install gcc make -y && \
        yum clean all

ENV D=/home/bridge

WORKDIR $D
COPY . $D/

RUN     make && \
        mv bridge /tmp/

# --- end build, create bridge runtime layer ---
FROM centos:8

LABEL io.k8s.display-name="SmartGateway AMQP Bridge" \
      io.k8s.description="Reads data from AMQP via proton and writes to a unix or UDP socket"

RUN yum install epel-release -y && \
        yum update -y --setopt=tsflags=nodocs && \
        yum install qpid-proton-c --setopt=tsflags=nodocs -y && \
        yum clean all && \
        rm -rf /var/cache/yum

COPY --from=builder /tmp/bridge /

ENTRYPOINT ["/bridge"]

