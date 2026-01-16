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

### RSync on Server

Transfering from added external storage to NFS dirs on the server

Mount external drive, e.g.

```console
mount /dev/sdd1 /mnt/ssd
```

Rsync a dir to one of the NFS drives, e.g.

```console
rsync -av --progress --partial --inplace /mnt/ssd/Video/Movies/ /mnt/usb1/movies/
```

If needed to run in the background, use tmux

```console
tmux new -s rsync
```

```console
rsync -av --progress --partial --inplace /mnt/ssd/Video/Movies/ /mnt/usb1/movies/
```

Then, detach with `CTRL + B`, then `d`. Reattach with `tmux attach -t rsync`.

### Mount NFS

```console
showmount -e 192.168.1.191
sudo mount -t nfs -o vers=4 192.168.1.191:/nfs /mnt/nfs/
```
