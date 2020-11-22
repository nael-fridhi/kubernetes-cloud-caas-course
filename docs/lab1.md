# Hands on LXC/LXD

- Documentation for linux containers: [https://linuxcontainers.org/](https://linuxcontainers.org/)
- Link where to find public LXC images: [https://us.images.linuxcontainers.org/](https://us.images.linuxcontainers.org/)

**N.B: This lab is tested on Ubuntu 18.04.5 LTS Bionic**

## 1. Setting up LXD

- Install LXD via snap: </br>
`sudo snap install lxd --channel=3.0/stable` </br>
or </br>
`sudo apt-get install lxd lxd-client`

- Ensure your user can execute lxc/lxd commands by adding it to the lxd group: </br>
`sudo usermod $USER -aG lxd`

- Initialize Lxd: </br>
`lxd init`
<pre><code>
Would you like to use LXD clustering? (yes/no) [default=no]: no
Do you want to configure a new storage pool? (yes/no) [default=yes]: yes
Name of the new storage pool [default=default]: default
Name of the storage backend to use (btrfs, ceph, dir, lvm, zfs) [default=zfs]: zfs
Create a new ZFS pool? (yes/no) [default=yes]: yes
Would you like to use an existing block device? (yes/no) [default=no]: yes
Path to the existing block device: /dev/nvme1n1
Would you like to connect to a MAAS server? (yes/no) [default=no]: no
Would you like to create a new local network bridge? (yes/no) [default=yes]: yes
What should the new bridge be called? [default=lxdbr0]: lxdbr0
What IPv4 address should be used? (CIDR subnet notation, “auto” or “none”) [default=auto]: auto
What IPv6 address should be used? (CIDR subnet notation, “auto” or “none”) [default=auto]: auto
Would you like LXD to be available over the network? (yes/no) [default=no]: yes
Address to bind LXD to (not including port) [default=all]: all
Port to bind LXD to [default=8443]: 8443
Trust password for new clients: anypassword
Again: anypassword
Would you like stale cached images to be updated automatically? (yes/no) [default=yes] yes
Would you like a YAML "lxd init" preseed to be printed? (yes/no) [default=no]: no
</pre></code>

- we can also initialize LXD using a preseed file </br>

## 2. Run LXD Containers

- Copy the image to the LXD server, ensuring to provide an alias: </br>
`lxc image copy images:alpine/3.10 local: --alias alpine` </br>
`lxc image copy ubuntu:16.04 local: --alias ubuntu` </br>

- Launch a new container, called alpine-test </br>
`lxc launch alpine alpine-test` </br> 
`lxc launch ubuntu ubuntu-test` </br>

- If the image doesn't not exist in the local server we can run </br>
`sudo lxc launch images:alpine/3.10 alpine-test`

- List container </br>
`lxc list` </br>

- Connect to container
`lxc exec my-alpine -- /bin/ash`
`echo hello world > hello.txt`

## 3. Configuring LXD image remote

<pre><code>
lxd init
lxc remote add lxd-share-remote 10.0.1.110
lxc image copy images:alpine/3.11 lxd-share: --alias alpine
lxc image copy ubuntu:16.04 lxd-share: --alias ubuntu
lxc image list
lxc image list lxd-share:
</pre></code>


## 4. LXD storage pool

- View the current storage configuration </br>
`lxc storage list` </br>

- Move the ZFS filesystem </br>
`lxc storage set default source /dev/nvme1n1` </br>

- An alternative is to the config file of the storage direclty </br>
`lxc storage edit default` </br>
<pre><code>
config:
size: 50GB
source: /dev/nvme1n1
zfs.pool_name: default
</pre></code>

- Create an experimental btrfs fs </br>
`lxc storage create experimental btrfs source=/dev/nvme2n1 size=5` </br>

- Create a directory filesystem </br>
`lxc storage create backups dir` </br>


## 5. Working with lxd containers 

<pre><code>
lxc list
lxc launch alpine web01
lxc launch alpine db01
lxc exec web01 -- apk update
lxc exec web01 -- apk add nginx
lxc exec web01 -- sh -c "rc-service nginx start && rc-update add nginx default"
# To ensure the db01 server is set to start before web01 in the event of a reboot.
lxc config set web01 boot.autostart.priority 50
lxc config set db01 boot.autostart.priority 90
</pre></code>

## 6. Managing file on LXD containers

lxc file pull <Container_name>/<Container_path_to_file> <host_path>

lxc file push <host_file_path> <Container_name>/<Container_path_to_file>

lxc file edit <Container_name>/<Container_path_to_file>


# 7. Creating LXD Container Profiles

- Profiles are like configuration files for instances (but they are saved in a database).

- create dbs profile from default profile: </br>
`lxc profile copy default dbs`</br>
`lxc profile edit dbs`

<pre><code>
lxc profile set dbs boot.autostart.priority 99
lxc profile set dbs boot.stop.priority 1
lxc profile set dbs snapshots.schedule "0 0 * * *"
</pre></code>

`lxc profile show dbs` </br>
- Run a container using a profile
`lxc launch alpine test -p dbs`</br>

- Add a profile to an existing container </br>
`lxc profile add db01 dbs`
- Remove a profile from an existing container </br>
`lxc profile remove db01 default`


## 8. Taking LXD Container Snapshots

`lxc snapshot web01 1.0`</br>
`lxc copy web01/1.0 web02`</br>
`lxc start web02`</br>

`lxc stop web02` </br>
`lxc delete web02` </br>
- delete snapshot </br>
`lxc delete web01/1.0` </br>

## 9. Configure a LXD image remote

To configure a LXD remote server which will contains our images and snapshots. we have to run as usual `lxd init` and configure our remote. </br>

`cat preseed_remote.yml | lxd init --preseed` </br>

## 10. Publishing a LXD Image

Add the remote server `lxd-share` to our server:
<pre><code>
lxc remote add lxd-share REMOTE_SERVER_IP
lxc list
lxc snapshot <CONTAINER_NAME> 1.0
lxc publish local:<CONTAINER_NAME>/1.0 lxd-share: --alias nginx-alpine
lxc launch lxd-share:nginx-alpine <CONTAINER_NAME_2>
lxc launch lxd-share:nginx-alpine web
</pre></code>

## 11. Using Distrobuilder to Create a LXD Image

To create LXD image from scratch we can use the buster yaml file in config folder
<pre><code>
sudo distrobuilder build-lxd buster.yaml
lxc image import lxd.tar.xz rootfs.squashfs --alias buster
</pre></code>