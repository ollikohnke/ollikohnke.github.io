# THIS IS A GUIDE HOW TO INSTALL PROXMOX ON DEBIAN 12

### https://pve.proxmox.com/wiki/Install_Proxmox_VE_on_Debian_12_Bookworm

### XFCE RDP CONFIGURATION https://learn.microsoft.com/en-us/azure/virtual-machines/linux/use-remote-desktop?tabs=azure-cli

### SSH Settings
```
sudo sed -i -e '/^\(#\|\)PermitRootLogin/s/^.*$/PermitRootLogin prohibit-password/' /etc/ssh/sshd_config
sudo sed -i -e '/^\(#\|\)PasswordAuthentication/s/^.*$/PasswordAuthentication no/' /etc/ssh/sshd_config
sudo sed -i -e '/^\(#\|\)KbdInteractiveAuthentication/s/^.*$/KbdInteractiveAuthentication no/' /etc/ssh/sshd_config
sudo sed -i -e '/^\(#\|\)ChallengeResponseAuthentication/s/^.*$/ChallengeResponseAuthentication no/' /etc/ssh/sshd_config
sudo sed -i -e '/^\(#\|\)MaxAuthTries/s/^.*$/MaxAuthTries 2/' /etc/ssh/sshd_config
sudo sed -i -e '/^\(#\|\)AllowTcpForwarding/s/^.*$/AllowTcpForwarding no/' /etc/ssh/sshd_config
sudo sed -i -e '/^\(#\|\)X11Forwarding/s/^.*$/X11Forwarding no/' /etc/ssh/sshd_config
sudo sed -i -e '/^\(#\|\)AllowAgentForwarding/s/^.*$/AllowAgentForwarding no/' /etc/ssh/sshd_config
sudo sed -i -e '/^\(#\|\)AuthorizedKeysFile/s/^.*$/AuthorizedKeysFile .ssh\/authorized_keys/' /etc/ssh/sshd_config
sudo sed -i '$a AllowUsers root opako' /etc/ssh/sshd_config
sudo sed -i '$a Port 22' /etc/ssh/sshd_config
```


### FAIL2BAN https://pve.proxmox.com/wiki/Fail2ban
```
# Update reposiyory and install fail2ban
apt update
apt install fail2ban

# Debian 12 fix
Add the following line to the [DEFAULT] section in /etc/fail2ban/paths-debian.conf (or in jail.local>[DEFAULT]):
sshd_backend = systemd

cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local

# /etc/fail2ban/jail.local
[proxmox]
enabled = true
port = https,http,8006
filter = proxmox
backend = systemd
maxretry = 3
findtime = 2d
bantime = 1h

# /etc/fail2ban/filter.d/proxmox.conf
[Definition]
failregex = pvedaemon\[.*authentication failure; rhost=<HOST> user=.* msg=.*
ignoreregex =
journalmatch = _SYSTEMD_UNIT=pvedaemon.service

# Restart service
systemctl restart fail2ban

# Test fail2ban
fail2ban-regex systemd-journal /etc/fail2ban/filter.d/proxmox.conf
```
### FAIL2BAN DEBIAN
```
sudo nano /etc/fail2ban/jail.local

[sshd]
backend=systemd
enabled = true
port = ssh
filter = sshd
logpath = /var/log/auth.log
maxretry = 3

sudo apt install python3-systemd

sudo systemctl restart fail2ban 
```

### FAIL2BAN XRDP 
```
sudo nano /etc/fail2ban/filter.d/xrdp.conf

[Definition]
failregex = AUTHFAIL: .* ip=<HOST>
ignoreregex =
datepattern = %%Y%%m%%d-%%H:%%M:%%S

sudo nano /etc/fail2ban/jail.local

[xrdp]
enabled = true
port = 3389
filter = xrdp
logpath = /var/log/xrdp-sesman.log
maxretry = 3
bantime = 1800

sudo systemctl restart fail2ban 
```

### Interfaces https://community.hetzner.com/tutorials/install-and-configure-proxmox_ve
```
# /etc/network/interfaces

auto lo
iface lo inet loopback

auto eth0
iface eth0 inet static
        address 45.148.31.113/32
        gateway 169.254.255.1
        #post-up iptables -t nat -A PREROUTING -i eth0 -p tcp -m multiport ! --dports 22,3389,8006 -j DNAT --to 10.10.10.2
        #post-down iptables -t nat -D PREROUTING -i eth0 -p tcp -m multiport ! --dports 22,3389,8006 -j DNAT --to 10.10.10.2


auto vmbr4
iface vmbr4 inet static
        address 192.168.10.1/24
        bridge-ports none
        bridge-stp off
        bridge-fd 0
        post-up   iptables -t nat -A POSTROUTING -s '192.168.10.0/24' -o eth0 -j MASQUERADE
        post-down iptables -t nat -D POSTROUTING -s '192.168.10.0/24' -o eth0 -j MASQUERADE
#NAT/Masq

```
### Enable port forwarding on host
```
# For IPv4 and IPv6:
sed -i 's/#net.ipv4.ip_forward=1/net.ipv4.ip_forward=1/' /etc/sysctl.conf
sed -i 's/#net.ipv6.conf.all.forwarding=1/net.ipv6.conf.all.forwarding=1/' /etc/sysctl.conf

# Apply the changes:
sysctl -p

# Check if the forwarding is active:
sysctl net.ipv4.ip_forward
sysctl net.ipv6.conf.all.forwarding
```




