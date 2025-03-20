# How to install and configure Incus on Debian 12
![Logo](00-Readme/incus.jpg)
### Set up users and groups and ids
```
uid=1000(opako)
gid=1001(opako)

groups=27(sudo)
groups=100(users)
groups=1001(opako)
groups=1002(admin)
```

### Set up remote dekstop https://cloud.google.com/architecture/chrome-desktop-remote-on-compute-engine
```
sudo apt install firefox-esr
```

### If you have no LXD installed on your system
```
# .bashrc
alias lxc='incus'
```
### From Debian https://wiki.debian.org/Incus
```
sudo apt install incus-base btrfs-progs dnsmasq-base
sudo incus admin init
sudo usermod -aG incus-admin opako
```
During installation incus will create default bridge **incusbr0**

### Configure firewall
```
# Install UFW and allow SSH
sudo apt install ufw
sudo ufw allow OpenSSH

# allow the guest to get an IP from the Incus host
sudo ufw allow in on incusbr0 to any port 67 proto udp
sudo ufw allow in on incusbr0 to any port 547 proto udp

# allow the guest to resolve host names from the Incus host
sudo ufw allow in on incusbr0 to any port 53

# allow the guest to have access to outbound connections
CIDR4="$(incus network get incusbr0 ipv4.address | sed 's|\.[0-9]\+/|.0/|')"
CIDR6="$(incus network get incusbr0 ipv6.address | sed 's|:[0-9]\+/|:/|')"
sudo ufw route allow in on incusbr0 from "${CIDR4}"
sudo ufw route allow in on incusbr0 from "${CIDR6}"

# Enable ufw
sudo ufw enable
sudo systemctl status ufw
sudo ufw status
```
### Create a custom cloud-init profile
```
lxc profile copy default cloud-init
lxc profile edit cloud-init
```
### Modify the configuration 
```
lxc launch images:debian/12/cloud vscode --profile cloud-init --config=user.user-data="$(cat /home/opako/user-data)"
```
### Add ssh-rsa to host userdir /home/opako/.ssh/id_rsa
```
nano /home/opako/.ssh/id_rsa
```
### Mount a directory from host home to guide
[rclone instructions](https://github.com/ollikohnke/notes/blob/main/06-Linux/03-Storage/GoogleDrive.md)
```
incus config device add vscode home disk source=/home/opako/gdrive path=/home/opako/gdrive shift=true
incus config device remove vscode home
```
### Install VS Code https://code.visualstudio.com/docs/setup/linux
```
sudo apt install ./<file>.deb
```
