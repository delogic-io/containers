FROM alpine:latest as puller

ARG TARGETARCH=${TARGETARCH:-amd64}
ARG TFLINT_VERSION=${TFLINT_VERSION:-0.44.1}
ARG TERRAFORM_VERSION=${TERRAFORM_VERSION:-1.1.0}
ARG TERRAGRUNT_VERSION=${TERRAGRUNT_VERSION:-0.43.1}

RUN wget https://releases.hashicorp.com/terraform/${TERRAFORM_VERSION}/terraform_${TERRAFORM_VERSION}_linux_${TARGETARCH}.zip && \
  unzip terraform_${TERRAFORM_VERSION}_linux_${TARGETARCH}.zip && \
  wget https://github.com/terraform-linters/tflint/releases/download/v${TFLINT_VERSION}/tflint_linux_${TARGETARCH}.zip && \
  unzip tflint_linux_${TARGETARCH}.zip && \
  wget https://github.com/gruntwork-io/terragrunt/releases/download/v${TERRAGRUNT_VERSION}/terragrunt_linux_${TARGETARCH} -O terragrunt


FROM python:3.11-alpine as tflint

LABEL org.opencontainers.image.authors="sre@delogic.io" \
      org.opencontainers.image.description="Tflint Docker image for pre-commit checks." \
      org.opencontainers.image.source="https://github.com/delogic-io/containers/tree/main/tflint" \
      org.opencontainers.image.base.name="python:3.11-alpine" \
      org.opencontainers.image.title="tflint" \
      org.opencontainers.image.vendor="Delogic" \
      org.opencontainers.image.licenses="Apache-2.0"

ARG TARGETARCH=${TARGETARCH:-amd64}
ARG PRECOMMIT_VERSION=${PRECOMMIT_VERSION:-3.0.2}

ENV OS_ARCH="${TARGETARCH}" \
    OS_DISTRO="alpine"

COPY --from=puller /terraform ./usr/bin/terraform
COPY --from=puller /tflint ./usr/bin/tflint
COPY --from=puller /terragrunt ./usr/bin/terragrunt

RUN apk add --no-cache git bash && \
  pip install pre-commit==${PRECOMMIT_VERSION} --no-cache-dir

SHELL ["/bin/bash", "-o", "pipefail", "-c"]
RUN APPS=( \
      "terraform" \
      "tflint" \
      "terragrunt" \
    ) && \
    for i in "${APPS[@]}"; do \
      chmod +x /usr/bin/${i}; done

CMD ["/bin/bash"]
