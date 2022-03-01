FROM ubuntu:20.04 as build-env

RUN apt-get update && apt-get install -y stress-ng

#######################################

FROM gcr.io/distroless/base

COPY --from=build-env /usr/bin/stress-ng /
COPY --from=build-env /usr/lib/x86_64-linux-gnu /usr/lib/x86_64-linux-gnu/

CMD ["/stress-ng"]
