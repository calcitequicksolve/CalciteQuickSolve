# CalciteQuickSolve Server 1.0.3 Beta with Docker or Podman

This example runs the browser UI and encrypted sync API on Linux amd64. It
uses local SQLite storage and a single owner login. Keep the deployment files
and data outside your Git checkouts.

Use `slyke/calcitequicksolve-server:v1.0.3` for this release or
`slyke/calcitequicksolve-server:beta` to follow the beta channel.
The Compose example pins `sha256:53726285d0dec2d915835fe36b6ca464405ee77a6ffd0d10506d7a5bacc8215f`.
[Docker Hub](https://hub.docker.com/r/slyke/calcitequicksolve-server/tags)

## Set up

Run these commands in Bash to download the example and create a password
verifier. Set `engine=podman` to use Podman. Enter the password you want to use
for the `owner` login.

```sh
mkdir -p "$HOME/cqs-server"
cd "$HOME/cqs-server"
base=https://raw.githubusercontent.com/calcitequicksolve/CalciteQuickSolve/v1.0.3/docs/hosted-server
curl -fsS "$base/compose.yaml" -o compose.yaml
curl -fsS "$base/config.example.json5" -o config.json5
engine=docker
image=slyke/calcitequicksolve-server:v1.0.3
"$engine" pull "$image"
read -rsp 'Owner password: ' owner_password
printf '\n'
owner_hash=$(printf '%s\n' "$owner_password" | "$engine" run --rm -i "$image" hash-password)
unset owner_password
(umask 077; printf 'CQS_PUBLIC_BASE_URL=http://127.0.0.1:8788\nCQS_OWNER_PASSWORD_HASH=%s\n' "$owner_hash" > server.env)
unset owner_hash
(umask 077
 read -rsp 'Signed Full/Notes activation: ' cqs_activation
 printf '\n'
 printf '%s\n' "$cqs_activation" > server.activation)
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
  -v "$PWD/server.activation:/run/secrets/cqs/activation:ro" \
  --read-only --tmpfs /tmp --cap-drop ALL --security-opt no-new-privileges \
  "$image"
```

## Activate the server

The example config loads the signed Full/Notes activation from
`license.file` at `/run/secrets/cqs/activation`. The container’s UID 1000
needs read access to the file. Restart after replacing the activation.
**Settings → License** shows the status, source, file path and any expiry.
A single activation can cover multiple server nodes. Clients use their own
activation. Without a valid activation file, the server opens in calculator
mode and stores calculator history and settings in the browser. The License
page displays license details. The server has no trial.

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
or reject incoming workspace requests, and link server peers. **Request
details** shows the requester, workspace and device information. A rejected
request can be retried on the client. Each device starts its first sync after
approval. For a server link, create a pairing code on the receiving server,
select workspaces on the initiating server, and accept the offered IDs on the
receiver. One link syncs both directions. Use the link form’s **Custom request
headers** for gateway credentials and **OAuth sign-in** for the peer’s native
OAuth registration. Set `peers.requireAuthenticatedPairing: true` on the
receiver to require a CQS sync credential or OAuth sign-in for new links.
Mirror a workspace
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
  HTTPS origin. Put additional HTTPS origins in `publicAliases` and route
  their hosts to the same Service. Preserve the original Host header. Register
  `/api/v1/auth/oauth/wui/callback` on every WUI OAuth origin. Native OAuth
  uses the primary origin’s callback.

### Mount the activation Secret

Copy the Secret template to your deployment directory and replace the value
under `stringData.activation` with the signed activation. The `|-` block
allows the token to span multiple lines. Indent each line by four spaces.
CQS strips whitespace before verifying the token.
[Kubernetes Secret template](kubernetes-license.yaml)

Apply it in the same namespace as the CQS Deployment.

```sh
kubectl apply -n YOUR_NAMESPACE -f kubernetes-license.yaml
```

In the Deployment’s pod template, add the following entries to the CQS
container’s `volumeMounts` and the pod’s `volumes`.

```yaml
volumeMounts:
  - name: cqs-server-license
    mountPath: /run/secrets/cqs
    readOnly: true
```

```yaml
volumes:
  - name: cqs-server-license
    secret:
      secretName: cqs-server-license
```

In the mounted server JSON5 config, use the following path.

```json5
license: { file: '/run/secrets/cqs/activation' },
```

Roll out the Deployment. Verify the mounted source under **Settings → License**.

## Remove a workspace from this server

In **Settings → Files & sync → Workspaces**, choose **Remove from server** and
confirm. This deletes the server copy and clears its peer workspace selections.
Clients and peers retain their copies. The next client sync attempt automatically
adds the workspace to **Settings → API → Workspace approvals** again. Approve it,
then start its first sync on the client to restore the server copy. Peer links
also need workspace approval again. Removal does not cascade through the mesh.
