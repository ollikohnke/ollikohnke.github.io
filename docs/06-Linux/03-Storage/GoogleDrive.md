# This is a guide how to setup google drive on linux
### How to mount google drive to linux (debian)
```
# Install Rclone
sudo apt install rclone
# Configurate > yes to all > webinteractive
rclone config
# List remotes
rclone listremotes
# Create folder to home directory
mkdir ~/gdrive
```
### Create a systemd service at host
```
sudo nano /etc/fuse.conf
sudo nano /etc/systemd/system/rclone-gdrive.service
```
```
[Unit]
Description=RClone Mount for Google Drive
After=network-online.target
Wants=network-online.target

[Service]
Type=simple
User=opako
Group=opako
ExecStart=/usr/bin/rclone mount gdrive: /home/opako/gdrive \
    --allow-other \
    --vfs-cache-mode full \
    --vfs-cache-max-size 10G
ExecStop=/usr/bin/fusermount -u /home/opako/gdrive
Restart=on-failure
RestartSec=10

[Install]
WantedBy=multi-user.target
```
```
sudo systemctl daemon-reload
sudo systemctl enable rclone-gdrive.service
sudo systemctl start rclone-gdrive.service
```

