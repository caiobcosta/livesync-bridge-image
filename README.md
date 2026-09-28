# livesync-bridge-image

Container image for [vrtmrz/livesync-bridge](https://github.com/vrtmrz/livesync-bridge),
the replicator for Self-hosted LiveSync (Obsidian ↔ CouchDB ↔ filesystem).

The upstream author does not publish an image (`docker-compose.yml` builds locally).
This repository does nothing but build the upstream source, unmodified, and publish it:

```
ghcr.io/caiobcosta/livesync-bridge:latest
ghcr.io/caiobcosta/livesync-bridge:<upstream sha, 12 chars>
```

Why: a registry-hosted image lets Unraid's dockerMan detect and apply updates, which
a `localhost/` build cannot do.

## How it works

`.github/workflows/build.yml` runs weekly (and on demand):

1. resolves upstream `HEAD`;
2. skips when `ghcr.io/…/livesync-bridge:<sha12>` already exists (nothing new upstream);
3. otherwise builds `linux/amd64` straight from the git context pinned to that SHA and pushes
   `latest` + `<sha12>`.

Run the workflow manually with `force: true` to rebuild the current commit (base-image updates).

Every image carries `org.opencontainers.image.revision` with the full upstream SHA, so
`docker inspect` always tells which commit is running.

## Trust

Nothing here patches upstream. Trust is the same as building locally with
`docker build https://github.com/vrtmrz/livesync-bridge.git#<sha>`, except the build runs
on a GitHub-hosted runner.
