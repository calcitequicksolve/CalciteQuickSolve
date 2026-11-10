# CalciteQuickSolve Server 1.0.2 Beta with Docker or Podman

This example runs the browser UI and encrypted sync API on Linux amd64. It
uses local SQLite storage and a single owner login. Keep the deployment files
and data outside your Git checkouts.

Use `slyke/calcitequicksolve-server:v1.0.2` for this release or
`slyke/calcitequicksolve-server:beta` to follow the beta channel.
The Compose example pins `sha256:570990855efb07d3ef88ea271cfffc6a05a38aa75fa787c99d357cc28b56acf3`.
[Docker Hub](https://hub.docker.com/r/slyke/calcitequicksolve-server/tags)

## Set up

Run these commands in Bash to download the example and create a password
verifier. Set `engine=podman` to use Podman. Enter the password you want to use
for the `owner` login.

```sh
mkdir -p "$HOME/cqs-server"
cd "$HOME/cqs-server"
base=https://raw.githubusercontent.com/calcitequicksolve/CalciteQuickSolve/v1.0.2/docs/hosted-server
curl -fsS "$base/compose.yaml" -o compose.yaml
curl -fsS "$base/config.example.json5" -o config.json5
engine=docker
image=slyke/calcitequicksolve-server:v1.0.2
"$engine" pull "$image"
read -rsp 'Owner password: ' owner_password
printf '\n'
owner_hash=$(printf '%s\n' "$owner_password" | "$engine" run --rm -i "$image" hash-password)
unset owner_password
(umask 077; printf 'CQS_PUBLIC_BASE_URL=http://127.0.0.1:8788\nCQS_OWNER_PASSWORD_HASH=%s\n' "$owner_hash" > server.env)
unset owner_hash
```

## Run with Docker Compose

```sh
docker compose up -d
curl -fsS http://127.0.0.1:8788/readyz
```

## Run with Docker or Podman

Use the same `config.json5`, `server.env`, `engine`, and `image` from setup.

```sh
"$engine" volume create cqs-data
"$engine" run -d --name cqs --restart unless-stopped \
  --env-file server.env -p 127.0.0.1:8788:8788 \
  -v "$PWD/config.json5:/config/config.json5:ro" -v cqs-data:/data \
  --read-only --tmpfs /tmp --cap-drop ALL --security-opt no-new-privileges \
  "$image"
```

## Activate the server

Sign in and enter a signed Full/Notes activation in the Web UI, or supply
`CQS_SERVER_LICENSE` through a deployment secret. To use a mounted secret,
set `CQS_SERVER_LICENSE_FILE` to the file’s path in the container.
**Settings → License** shows the source and status. A single activation can
cover multiple server nodes. Clients use their own activation.

## Use workspaces and sync

Once the readiness check succeeds, open <http://127.0.0.1:8788> on the
container host and sign in as `owner`. On the updated Beta image, activate
workspaces under **Workspaces** to see their notes together. Choose a default
workspace for new notes. Workspaces that use server sign-in open after login.
Workspaces with a separate vault password require their own unlock.

For remote clients, set up an HTTPS reverse proxy and set
`CQS_PUBLIC_BASE_URL` to its external origin. In the Windows or Android beta,
add that URL under **Settings → Files & sync → Server & encrypted sync**. With
API sync enabled, clients sync their linked workspaces using their credentials
and workspace keys. WUI activation is a browser preference. WebSocket
notifications tell clients when to fetch changes over HTTP.

Use **Settings → API** to create read/write sync credentials, approve
incoming workspace IDs, and link server peers. Each client device starts its
first sync after approval. For a server link, create a pairing code on the
receiving server, select workspaces on the initiating server, and accept the
offered IDs on the receiver. One link syncs both directions. Mirror a workspace
whose keys have already rotated to a new server before starting the device’s
first sync there. Use **Settings → MCP** for agent controls and read-only mode.

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
