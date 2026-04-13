---
publish: true
aliases:
  - SSH
  - SSH key
title: Secure Shell Protocol
modified: 2026-04-12T02:54:39.458-04:00
tags:
  - tool/cli
  - tool/network
---

> [!abstract] Secure Shell Protocol
> A encrypted network protocol for operating network services securely over an unsecured network.

---

## Install

```shell
# arch
sudo pacman -S openssh
sudo systemctl start sshd
sudo systemctl enable sshd
```

---

## Usage

| Action        | Command                                         | Notes                         |
| ------------- | ----------------------------------------------- | ----------------------------- |
| Connect       | `ssh <user@host>`                               |                               |
| Custom Port   | `ssh -p <port> <user@host>`                     | default port is 22            |
| Identity File | `ssh -i ~/.ssh/<key> <user@host>`               | will check all keys otherwise |
| Copy Key      | `ssh-copy-id -i ~/.ssh/<key>.pub <user>@<host>` |                               |

---

## SSH Key

SSH keys provide a secure, passwordless way to authenticate. **Ed25519** algorithm is the current standard for security and performance.

1. Generate key pair.

```shell
ssh-keygen -t ed25519 -C "user@machine"
```

- Comment `-C "..."`: optional but user and machine name is helpful.
- File name: optional, default is `id_ed25519`.
- Password: optional but highly recommended.
- Public Key (share this): `~/.ssh/id_ed25519.pub`.
- Private Key (keep secret): `~/.ssh/id_ed25519`.

2. Distribute to server.

```shell
ssh-copy-id -i ~/.ssh/<key>.pub <user>@<server-ip>

# ex. ssh-copy-id -i ~/.ssh/id_ed25519.pub <user>@<server-ip>
```
