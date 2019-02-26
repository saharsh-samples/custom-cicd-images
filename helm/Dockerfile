FROM fedora:29
RUN curl -o /kubectl https://storage.googleapis.com/kubernetes-release/release/v1.13.3/bin/linux/amd64/kubectl && \
    chmod a+rx /kubectl

FROM alpine/helm:2.12.3
COPY --from=0 /kubectl /usr/local/bin/kubectl
