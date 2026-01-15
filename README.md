# Automated Setup of NFS Server

This playbook is used to setup a Debian Linux machine as an NFS server for my homelab.

## Setup

Install deps

```console
uv sync
source .venv/bin/activate

prek install -f
```

Install ansible-galaxy deps

```console
ansible-galaxy install -r ./collections/requirements.yaml
```

Settings are in `ansible.cfg` with adapted inventory path to match.

## Prepare Cluster

First, copy over ssh key

```console
ssh-copy-id -i ~/.ssh/id_ed25519.pub root@cube06
```

### Get Storage Disk UUIDs

```console
ansible nfs_servers -b -m shell -a "lsblk -f"
```

The UUIDs should be added to the host_vars.

## Create NFS Server

Start provisioning of the cluster using the following command:

```bash
ansible-playbook playbooks/nfs-server.yaml -i inventory.yaml
```

## Reset

WIP

## Usage

Transfering from external storage to NFS dirs

Mount external drive, e.g.

```console
mount /dev/sdd1 /mnt/ssd
```

Rsync a dir to one of the NFS drives, e.g.

```console
rsync -av --progress --partial --inplace /mnt/ssd/Video/Movies/ /mnt/usb1/movies/
```

If needed to run in the background, use nohup

```console
nohup rsync -av --progress --partial --inplace /mnt/ssd/Video/Movies/ /mnt/usb1/movies/ > rsync.log 2>&1 &
```
