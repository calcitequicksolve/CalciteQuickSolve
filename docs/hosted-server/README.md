# CalciteQuickSolve Server 1.0.1 Beta with Docker

This example runs the browser UI and encrypted sync API on Linux amd64. It
uses local SQLite storage and a single owner login. Keep the deployment files
and data outside your Git checkouts.

The published `slyke/calcitequicksolve-server:1.0.1` image has digest
`sha256:a6b99a75e4a53425a7f541f7739ff29cdc5968da5bc43fc7a7f6dd202a52078c`.
[Docker Hub](https://hub.docker.com/r/slyke/calcitequicksolve-server/tags)

## Set up

Run these commands in Bash to download the example and create a password
verifier. Enter the password you want to use for the `owner` login.

```sh
mkdir -p "$HOME/cqs-server"
cd "$HOME/cqs-server"
base=https://raw.githubusercontent.com/calcitequicksolve/CalciteQuickSolve/v1.0.1/docs/hosted-server
curl -fsS "$base/compose.yaml" -o compose.yaml
curl -fsS "$base/config.example.json5" -o config.json5
image=slyke/calcitequicksolve-server:1.0.1
docker pull "$image"
read -rsp 'Owner password: ' owner_password
printf '\n'
owner_hash=$(printf '%s\n' "$owner_password" | docker run --rm -i "$image" hash-password)
unset owner_password
(umask 077; printf 'CQS_PUBLIC_BASE_URL=http://127.0.0.1:8788\nCQS_OWNER_PASSWORD_HASH=%s\n' "$owner_hash" > server.env)
unset owner_hash
```

## Run with Docker Compose

```sh
docker compose up -d
curl -fsS http://127.0.0.1:8788/readyz
```

Once the readiness check succeeds, open <http://127.0.0.1:8788> on the Docker
host and sign in as `owner`. The browser asks for a separate workspace vault
password. To connect a remote client, first set up an HTTPS reverse proxy and
set `CQS_PUBLIC_BASE_URL` to its external origin. In the Windows or Android
beta, add that URL under **Settings → Files & sync → Server & encrypted sync**.

## Run with Docker

Use the same `config.json5` and `server.env` with Docker directly.

```sh
docker volume create cqs-data
docker run -d --name cqs --restart unless-stopped \
  --env-file server.env -p 127.0.0.1:8788:8788 \
  -v "$PWD/config.json5:/config/config.json5:ro" -v cqs-data:/data \
  --read-only --tmpfs /tmp --cap-drop ALL --security-opt no-new-privileges \
  slyke/calcitequicksolve-server:1.0.1
```

## Container settings for Kubernetes

- Run one replica on a Linux amd64 node. The container listens on TCP `8788`
  and runs as UID/GID `1000`. Use `/livez` and `/readyz` for HTTP probes.
- Mount the example config at `/config/config.json5` and inject
  `CQS_OWNER_PASSWORD_HASH` from a Secret. Provide writable `/data` and
  `/tmp` mounts with access for UID/GID `1000`.
- Persist `/data` on local or block storage for SQLite. For NFS or other
  network filesystems, configure PostgreSQL for metadata before startup.
- Terminate TLS at your ingress and set `CQS_PUBLIC_BASE_URL` to the external
  HTTPS origin. Use that origin for the browser, phone and PC.
