# Docker

## Install Docker

[Reference](https://docs.docker.com/engine/install/ubuntu/).

```bash
sudo -i
apt update
apt -y install apt-transport-https ca-certificates curl gnupg lsb-release
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
echo \
  "deb [arch=amd64 signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | tee /etc/apt/sources.list.d/docker.list > /dev/null
apt update
apt -y install docker-ce docker-ce-cli containerd.io
```

Check.

```bash
sudo docker run hello-world
```

## Save image to tar file

```bash
docker save -o <filename>.tar <name>:<version>
```

## Load image from tar file

```bash
docker load -i <filename>.tar
```

## Common run options

| Options | Description
| -- | --
| `-it` | Attach to pseudo-tty
| `--rm` | Clean up container upon exit
| `--name` | Container name
| `--ipc=host` | Shared memory
| `--gpus all` | All GPUs passthrough
