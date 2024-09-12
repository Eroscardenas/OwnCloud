FROM hashicorp/vault

ENV SKIP_SETCAP=true

VOLUME /vault/logs
VOLUME /vault/file

USER root
RUN setcap cap_ipc_lock=+ep /bin/vault

USER vault
EXPOSE 8200
