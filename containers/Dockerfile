FROM registry.access.redhat.com/ubi8/ubi:latest AS builder
RUN dnf -y install make gcc git rpm-build && dnf clean all
COPY . /tmp/
RUN cd /tmp && make rpm

# End of build
FROM registry.access.redhat.com/ubi8/ubi-minimal:latest
LABEL org.opencontainers.image.source https://github.com/gs-kamnas/cntlm
RUN microdnf -y install shadow-utils && microdnf clean all
RUN groupadd -g 99 cntlm && \
    useradd -r -m -g cntlm -d /run/cntlm -s /sbin/nologin -c "cNTLM daemon" cntlm && \
    chown cntlm:cntlm /run/cntlm

RUN mkdir -p /usr/share/containers/packages
COPY --from=builder /tmp/cntlm-*.rpm /usr/share/containers/packages/

RUN rpm -iv --nodeps /usr/share/containers/packages/*.rpm

ENTRYPOINT [ "/usr/sbin/cntlm" ]