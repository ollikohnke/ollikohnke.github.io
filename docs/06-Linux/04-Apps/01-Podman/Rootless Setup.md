## How to setup Rootless podman on linux

Initial setup of VM
```
adduser opako
usermod -aG sudo opako
rsync --archive --chown=opako:opako ~/.ssh /home/opako

apt update
apt upgrade

apt install fail2ban
printf "[sshd]\nenabled = true\nbanaction = iptables-multiport" > /etc/fail2ban/jail.local
systemctl enable fail2ban

apt install ufw
systemctl enable ufw
systemctl start ufw
ufw allow OpenSSH
ufw enable

sed -i -e '/^\(#\|\)PermitRootLogin/s/^.*$/PermitRootLogin no/' /etc/ssh/sshd_config
sed -i -e '/^\(#\|\)PasswordAuthentication/s/^.*$/PasswordAuthentication no/' /etc/ssh/sshd_config
sed -i -e '/^\(#\|\)KbdInteractiveAuthentication/s/^.*$/KbdInteractiveAuthentication no/' /etc/ssh/sshd_config
sed -i -e '/^\(#\|\)ChallengeResponseAuthentication/s/^.*$/ChallengeResponseAuthentication no/' /etc/ssh/sshd_config
sed -i -e '/^\(#\|\)MaxAuthTries/s/^.*$/MaxAuthTries 2/' /etc/ssh/sshd_config
sed -i -e '/^\(#\|\)AllowTcpForwarding/s/^.*$/AllowTcpForwarding no/' /etc/ssh/sshd_config
sed -i -e '/^\(#\|\)X11Forwarding/s/^.*$/X11Forwarding no/' /etc/ssh/sshd_config
sed -i -e '/^\(#\|\)AllowAgentForwarding/s/^.*$/AllowAgentForwarding no/' /etc/ssh/sshd_config
sed -i -e '/^\(#\|\)AuthorizedKeysFile/s/^.*$/AuthorizedKeysFile .ssh\/authorized_keys/' /etc/ssh/sshd_config
sed -i '$a AllowUsers opako podman' /etc/ssh/sshd_config

reboot
```

Add new user and copy public SSH key to access with nutty
```
sudo adduser podman
sudo rsync --archive --chown=podman:podman ~/.ssh /home/podman
```

Attach hetzner volume to VM from cloud console
```
sudo nano /etc/fstab

/dev/disk/by-id/scsi-0HC_Volume_101544923 /mnt/vol-opako ext4 discard,nofail,defaults 0 0
/mnt/vol-opako /home/opako/vol-opako none bind
/mnt/vol-opako /home/podman/vol-opako none bind

sudo mkdir /mnt/vol-opako
mkdir /home/opako/vol-opako
mkdir /home/podman/vol-opako

sudo groupadd mnt-data
sudo usermod -aG mnt-data opako
sudo usermod -aG mnt-data podman

sudo chown -R opako:mnt-data vol-opako/
sudo chmod -R g+srwx vol-opako/

sudo mount -all
```

Install VSCODE standalone
```
curl -Lk 'https://code.visualstudio.com/sha/download?build=stable&os=cli-alpine-x64' --output vscode_cli.tar.gz
tar -xf vscode_cli.tar.gz
screen -S code
./code tunnel
screen CTRL A+D
```

Install podman
```
### Add unstable repository

echo "deb https://deb.debian.org/debian unstable main" > /etc/apt/sources.list.d/unstable-debian.list
sudo apt update
sudo apt install podman
sudo apt install newuidmap
sudo apt install newgidmap
sudo apt install passt
sudo apt install aardvark-dns


sudo rm /etc/apt/sources.list.d/unstable-debian.list
sudo apt update
sudo apt install podman-compose


### Configure podman for rootless to allow binding to ports 443 and higher https://github.com/containers/podman/blob/main/docs/tutorials/rootless_tutorial.md

sudo nano /etc/sysctl.d/99-mysettings.conf
net.ipv4.ip_unprivileged_port_start=443
sudo sysctl --system

### Configure pasta https://matduggan.com/replace-compose-with-quadlet/
### https://github.com/eriksjolund/podman-networking-docs

nano /home/podman/.config/containers/containers.conf
[network]
pasta_options = ["-a", "10.0.2.0", "-n", "24", "-g", "10.0.2.2", "--dns-forward", "10.0.2.3"]


### Modify registries

sudo nano /etc/containers/registries.conf
unqualified-search-registries = ["docker.io", "ghcr.io"]

### Socket activation
https://github.com/containers/podman/blob/main/docs/tutorials/socket_activation.md

### Start portainer

podman volume create portainer_data
podman network create external
podman-compose up -d
```

> [!NOTE]
> Useful information that users should know, even when skimming content.It is also helpful to distinguish between running Podman as a rootless user, and a container which   is built to run rootless. If the container you're trying to run has a USER which is not root, then when mounting volumes you must use --userns=keep-id. This is because the container user would not be able to become root and access the mounted volumes.








