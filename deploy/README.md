# Deployment from this fork

This branch starts at LedgerSMB's published `1.13.8` release. `master` remains
the upstream development branch. Changes intended for the server should be
reviewed and merged into `deploy/1.13.8`.

The GitHub Actions workflow builds the UI from this fork, overlays the checked
out application onto the official `1.13.8` runtime image, and publishes an
image to `ghcr.io/joshconway00/ledgersmb`. Each build has a commit SHA tag and
an immutable registry digest. Deploy by digest after the build succeeds.

On the server, use the official `ledgersmb/ledgersmb-docker` 1.13 Compose file
as the starting point. Keep its PostgreSQL service, internal network, and
`pgdata` volume. Replace its application image with the fork image digest.
Replace the example database password with a server-only secret. Replace the
evaluation host port mappings with a loopback-only mapping to container port
80, and terminate HTTPS through the existing private Tailscale route. Do not
publish PostgreSQL or the application HTTP port directly.

Before each update, back up PostgreSQL and record the currently running image
digest. Pull and start the new digest, check `/setup.pl` and `/login.pl` through
the private HTTPS route, then confirm database access. If the update fails,
restore the previous image digest; restore the database backup if the update
changed its schema. Keep backups outside the Compose volume.

The image build currently targets `linux/amd64`, matching the server. The
official runtime supplies dependencies for LedgerSMB 1.13. If a fork change
adds a Perl dependency, update and test the runtime build before deploying it.
