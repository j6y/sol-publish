---
publish: true
title: Backrest
modified: 2026-04-13T05:15:21.169-04:00
tags:
  - tool/backup
  - homelab
---

> [!abstract] Backrest
> Backup solution that uses a [Restic](https://restic.net/) back-end and features a easy-to-use WebUI.
>
> Documentation: https://garethgeorge.github.io/backrest/
> Repository: https://github.com/garethgeorge/backrest

---

## Install with Bare-Metal

1. Copy link to latest release `backrest_Linux_x86_64.tar.gz` from [git repo](https://github.com/garethgeorge/backrest/releases/tag/v1.10.1).
2. Download release to server.

```shell
wget <url>

# <url> ex. https://github.com/garethgeorge/backrest/releases/download/v1.10.1/backrest_Linux_x86_64.tar.gz
```

3. Run Backrest install script.

```shell
mkdir backrest && tar -xzvf backrest_Linux_x86_64.tar.gz -C backrest
cd backrest && ./install.sh
```

4. Edit Backrest systemd file to enable remote connections.

```shell
sudo systemctl edit backrest
```

Add these two lines after the first comment.

```
[Service]
Environment="BACKREST_PORT=0.0.0.0:9898"
```

5. Open Backrest `http://<local-server-ip>:9898`.
6. Enter a unique instance id then click `Ok`.

---

Note: A repository is the storage location where backup data is kept.

## Setup SFTP Repository

1. Add remote server to `.ssh/config`.

```
Host <remote-server-name>
	HostName <remote-server-hostname>
	User <remote-server-username>
	Port 23
```

- Host: choose any name.
- (optional) Port: defaults to 22, [Hetzner Storage Box](https://www.hetzner.com/storage/storage-box/) uses 23.

2. Create new [[Secure Shell Protocol#SSH Key|SSH Key]] (if needed) and copy it to remote server.

```shell
ssh-keygen -t ed25519 -C "user@machine"
ssh-copy-id -i ~/.ssh/id_ed25519 <remote-server-name>

# for hetzner storage box use -s to specify sftp
ssh-copy-id -i ~/.ssh/id_ed25519 -s <remote-server-name>
```

3. Open Backrest `http://<local-server-ip>:9898`.
4. Select **Add Repo**.
5. Enter repository name as remote server name.
6. Enter repository url as sftp path on remote server.

```
sftp://<remote-server-name>:/<remote-repo-path>

# ex. sftp://hetzner:/proxmox-backup
```

7. Click **Generate** for password. Save this password securely, it is needed to decrypt data.
8. Click **Test Configuration** then **Submit**.

---

## Setup Plan

1. Select **Add Plan**.
2. Enter plan name, something like `<local-folder-name>-to-<remote-server-name`.
3. Select appropriate repository.
4. Select **Add Path** and enter path to local directory to be backed up. This could be a remote [[NFS]] or [[SMB]] share.
5. Set backup schedule as **Cron: UTC: Every Day at 12:00** and set retention policy as **By Count: 30**. Free to change as needed.
6. Click **Submit** then **Backup Now** to run initial backup.

---

## Restore Backup

1. Select the plan and backup snapshot to restore from.
2. Expand **Snapshot Browser** dropdown.
3. Press **Download** (for new directory) or **Restore to Path** (for original directory) on the desired file or folder that needs to be restored.
