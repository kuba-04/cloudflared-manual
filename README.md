# cloudflared-manual

Here are the essential steps with commands and examples to set up a Cloudflare Tunnel using Docker Compose:

### Step 1: Authorize `cloudflared` with your Cloudflare account

```bash
docker run --rm -v ~/cloudflared:/home/nonroot/.cloudflared cloudflare/cloudflared:2025.2.0 tunnel login
```

### Step 2: Create a tunnel

```bash
docker run --rm -v ~/cloudflared:/home/nonroot/.cloudflared cloudflare/cloudflared:2025.2.0 tunnel create <tunnel-name>
```

Example: `docker run --rm -v ~/cloudflared:/home/nonroot/.cloudflared cloudflare/cloudflared:2025.2.0 tunnel create my-tunnel`

### Step 3: Add DNS records mapping hostnames to the tunnel

```bash
docker run --rm -v ~/cloudflared:/home/nonroot/.cloudflared cloudflare/cloudflared:2025.2.0 tunnel route dns <tunnel-name> <hostname>
```

Example: `docker run --rm -v ~/cloudflared:/home/nonroot/.cloudflared cloudflare/cloudflared:2025.2.0 tunnel route dns my-tunnel home.example.com`

### Step 4: Copy the credentials file to the server

Copy the `~/cloudflared/<tunnel-id>.json` file to the server.

### Step 5: Use `docker-compose.yml` and `cloudflared-config.yml` files

Create a `docker-compose.yml` file with the following content:

```yaml
version: '3'
services:
  cloudflared:
    image: cloudflare/cloudflared:2025.2.0
    volumes:
      - ./cloudflared-config.yml:/etc/cloudflared/config.yml
      - ./cloudflared-credentials.json:/etc/cloudflared/credentials.json
    command: tunnel
```

Create a `cloudflared-config.yml` file with the following content:

```yaml
tunnel: my-tunnel
```

### Step 6: Run `docker-compose up` and check the logs for `cloudflared`

```bash
docker-compose up
```

Check the logs for `cloudflared` to make sure it started the tunnel.

### Additional Steps

* Add a new service: put a password on it using Cloudflare Access.
* In Kubernetes, use a `k8s Deployment` and a `ConfigMap` instead of `docker-compose.yml`.

Note: Make sure to replace `<tunnel-name>` and `<hostname>` with your actual values, and update the `docker-compose.yml` and `cloudflared-config.yml` files accordingly.
