FROM fedora:30
RUN curl -o /kubectl https://storage.googleapis.com/kubernetes-release/release/v1.14.0/bin/linux/amd64/kubectl && \
    chmod a+rx /kubectl

FROM alpine/helm:2.13.1
COPY --from=0 /kubectl /usr/local/bin/kubectl
