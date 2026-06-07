FROM ubuntu:22.04

ENV WORK_DIR=/autoinstall
RUN mkdir -p $WORK_DIR

COPY to_docker/config $WORK_DIR/config
COPY to_docker/Makefile $WORK_DIR/
COPY to_docker/ubuntu-22.04.3-live-server-amd64.iso $WORK_DIR/

RUN apt update
RUN apt install -y make fdisk xorriso

WORKDIR $WORK_DIR
RUN make
# end