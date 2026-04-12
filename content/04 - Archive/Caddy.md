---
publish: true
title: Caddy
created: 2026-04-11T16:27:52.427-04:00
modified: 2026-04-11T17:28:48.549-04:00
tags:
  - tool/web-server
  - homelab
---

> [!abstract] Caddy
> A high-performance web server and reverse proxy written in Go.
>
> Documentation: https://caddyserver.com/docs/
> Repository: https://github.com/caddyserver/caddy

---

## Install

### Bare-Metal

Run the following commands.

```shell
# debian or ubuntu
sudo apt install -y debian-keyring debian-archive-keyring apt-transport-https curl
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/gpg.key' | sudo gpg --dearmor -o /usr/share/keyrings/caddy-stable-archive-keyring.gpg
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/debian.deb.txt' | sudo tee /etc/apt/sources.list.d/caddy-stable.list
chmod o+r /usr/share/keyrings/caddy-stable-archive-keyring.gpg
chmod o+r /etc/apt/sources.list.d/caddy-stable.list
sudo apt update
sudo apt install caddy
```

### Docker

1. Create a `caddy/compose.yaml` file.

```yaml
services:
  caddy:
    image: caddy:latest
    container_name: caddy
    restart: unless-stopped
    ports:
      - "80:80"
      - "443:443"
      - "443:443/udp"
    volumes:
      - ./Caddyfile:/etc/caddy/Caddyfile
        ./srv:/var/www/html
      - caddy_data:/data
      - caddy_config:/config
    command:
      [
        "caddy",
        "run",
        "--config",
        "/etc/caddy/Caddyfile",
        "--adapter",
        "caddyfile",
        "--watch",
      ]

volumes:
  caddy_data:
  caddy_config:
```

2. Run the container.

```shell
cd caddy
docker compose up -d
```

---

## Setup Cloudflare

1. Open https://dash.cloudflare.com/profile/api-tokens.
2. Click **Create Token**.
3. Create two zone permissions.
   1. Zone - Zone - Read
   2. Zone - DNS - Edit
4. Create at least one zone permission.
   1. Include - Specific zone - `<your-domain>`
5. Click **Confirm**. Then save the api-token securely.

![[00 - Meta/Images/Pasted image 20260411165519.png#invert]]

6. Add Cloudflare snippet to the beginning of Caddyfile.

```Caddyfile
(cloudflare) { 
    tls {
        dns cloudflare <CLOUDFLARE_API_TOKEN>
        resolvers 1.1.1.1
    }
}
```

2. Modify all entries to include Cloudflare snippet.

```
<domain> {
    import cloudflare
    ...
}
```

### Modify Bare-Metal Install

1. Install xcaddy.

```shell
sudo apt install -y debian-keyring debian-archive-keyring apt-transport-https
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/xcaddy/gpg.key' | sudo gpg --dearmor -o /usr/share/keyrings/caddy-xcaddy-archive-keyring.gpg
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/xcaddy/debian.deb.txt' | sudo tee /etc/apt/sources.list.d/caddy-xcaddy.list
sudo apt update
sudo apt install xcaddy
```

2. Install Go.

```shell
wget https://go.dev/dl/go1.26.1.linux-amd64.tar.gz
rm -rf /usr/local/go && tar -C /usr/local -xzf go1.26.1.linux-amd64.tar.gz
export PATH=$PATH:/usr/local/go/bin
go version
```

3. Build binary with Cloudflare module.

```shell
xcaddy build --with github.com/caddy-dns/cloudflare
sudo dpkg-divert --divert /usr/bin/caddy.default --rename /usr/bin/caddy
sudo mv ./caddy /usr/bin/caddy.custom
sudo update-alternatives --install /usr/bin/caddy caddy /usr/bin/caddy.default 10
sudo update-alternatives --install /usr/bin/caddy caddy /usr/bin/caddy.custom 50
sudo systemctl restart caddy
```

### Modify Docker Install

Replace

```yaml
    image: caddy:latest
```

With

```yaml
    build:
      context: .
      dockerfile: Dockerfile
```

---

Note: For https challenges, port 80 must be publicly accessible on the server. Otherwise [[#Setup Cloudflare]].

## Reverse Proxy

- HTTP Proxy:

```Caddyfile
qbittorrent.home.nodusk.me {
    import cloudflare
    reverse_proxy media:8080
}
```

- HTTPS Proxy:

```Caddyfile
dusk.home.nodusk.me {
    import cloudflare
    reverse_proxy 192.168.2.101:8006 {
        transport http {
            tls
            tls_insecure_skip_verify
        }
    }
}
```

## Web Server

1. Clone static website files to server.

```shell
git clone <git-repo-url> /var/www/html
# ex. git clone https://gitea.nodusk.me/jay/mercury-publish.git /var/www/html
```

2. Edit Caddyfile `/etc/caddy/Caddyfile`.

```Caddyfile
:80 {
    root * /var/www/html
    file_server
}
```

3. Reload Caddy.

```shell
systemctl restart caddy
```

5. Open server at http://localhost or `http://<server-ip>:80` if hosted remotely.
6. (optional) Use [[Pangolin]], [[#Reverse Proxy|Caddy]], or another reverse proxy to add domain name and remote access.
