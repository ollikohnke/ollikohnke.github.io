# LXD

## Installing

```
sudo groupmod -aG lxd opako

lxd init

lxdbr0 = 10.0.0.1/24

lxc config set core.https_address :8443