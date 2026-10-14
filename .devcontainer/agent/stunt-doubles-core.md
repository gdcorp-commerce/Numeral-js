## stunt-doubles infrastructure

The root CA is trusted system-wide in this devcontainer. HTTPS calls to all mocked
service hostnames succeed without `--insecure` or certificate errors.

**CA certificate** — `/usr/local/share/stunt-doubles/ca/root-ca.crt`

Trust the CA in another container or process:
```bash
# Node.js (already set via NODE_EXTRA_CA_CERTS)
echo $NODE_EXTRA_CA_CERTS    # → /usr/local/share/stunt-doubles/ca/root-ca.crt

# curl
curl --cacert /usr/local/share/stunt-doubles/ca/root-ca.crt https://sso.godaddy.com/...

# Mount into a Docker container
docker run --rm \
  -v /usr/local/share/stunt-doubles/ca/root-ca.crt:/usr/local/share/ca-certificates/stunt.crt \
  ...
```

**Chrome / Chromium trust**

Chrome ignores the system CA store. Instead, a wrapper script is installed at
`/usr/local/bin/chromium` (and `chromium-browser`, `google-chrome`, `google-chrome-stable`).
It reads the root CA's SPKI hash from `/usr/local/share/stunt-doubles/ca/spki-hash` and
injects `--ignore-certificate-errors-spki-list` automatically on every Chrome launch.
No changes needed to how Chrome is started.

**Docker network** — `stunt-doubles-net`

Add a container to the mock network so it can reach service hostnames:
```bash
docker run --rm --network stunt-doubles-net ...
```

**Docker volume** — `stunt-doubles-certs`

Contains the root CA and all service TLS certs. Mount read-only in containers that
need to trust or present certificates:
```bash
-v stunt-doubles-certs:/certs:ro
# root CA:  /certs/root-ca.crt
# service:  /certs/<service-name>/cert.pem  and  /certs/<service-name>/key.pem
```

**Creating a standalone cert for your app server**

Use `stunt cert create` to generate a cert + key signed by the shared root CA and
write them to a directory your app can read:
```bash
stunt cert create <name> <hostname> <outdir>

# Example — wildcard cert for a local dev server:
stunt cert create local-commerce "*.local.commerce.test-godaddy.com" /workspace/cert
# Outputs:
#   /workspace/cert/_.local.commerce.test-godaddy.com.crt
#   /workspace/cert/_.local.commerce.test-godaddy.com.key
#   /workspace/cert/root-ca.crt
```

After creating the cert, register the hostname so the devcontainer can resolve it:
```bash
# Start your service on stunt-doubles-net, then get its bridge IP
IP=$(docker inspect my-svc \
  --format '{{(index .NetworkSettings.Networks "stunt-doubles-net").IPAddress}}')

# Add-only: errors if the hostname already points elsewhere
stunt host add api.example.com "$IP"

# List all stunt-managed /etc/hosts entries
stunt host list
```
