FROM ubuntu:20.04 as build-env

RUN apt-get update && apt-get install -y curl

RUN curl -s https://packagecloud.io/install/repositories/akopytov/sysbench/script.deb.sh | bash && \
        apt -y install sysbench

RUN which sysbench

#######################################

FROM gcr.io/distroless/base

COPY --from=build-env /usr/bin/sysbench /
COPY --from=build-env /usr/lib/x86_64-linux-gnu /usr/lib/x86_64-linux-gnu/

WORKDIR /working

CMD ["/sysbench"]
