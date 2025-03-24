# LXD

## Installing

```
sudo groupmod -aG lxd opako

lxd init

lxdbr0 = 10.0.0.1/24

lxc config set core.https_address :8443

lxc alias add login 'exec @ARGS@ --mode interactive -- bash -xc {exec,login,-p,-f,$USER}'

# Root login
lxc login container
# User login
lxc login container --env USER=ubuntu
```