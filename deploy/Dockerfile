# Keep LedgerSMB's official 1.13 runtime, including its nginx and Starman setup.
# The checked-out fork supplies the application files and built UI assets.
FROM ghcr.io/ledgersmb/ledgersmb:1.13.8

ARG VCS_REF
LABEL org.opencontainers.image.source="https://github.com/JoshConway00/LedgerSMB" \
      org.opencontainers.image.revision="${VCS_REF}"

USER root
COPY --chown=www-data:www-data . /srv/ledgersmb/
RUN test -n "$(find /srv/ledgersmb/UI/js -type f -name '*.js' -print -quit)" \
    && test -f /srv/ledgersmb/cpanfile \
    && test -f /srv/ledgersmb/bin/ledgersmb-server.psgi
# The official proxy image starts s6 as root and drops privileges inside its
# nginx and Starman service scripts. Keep that runtime user unchanged.
