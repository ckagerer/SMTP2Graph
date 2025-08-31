# SMTP2Graph

> SMTP2Graph is a robust, versatile and lightweight multiplatform application that will run an SMTP server which relays messages over Microsoft 365/Exchange Online using the Microsoft Graph API.

[![GitHub Last Release](https://img.shields.io/github/v/release/SMTP2Graph/SMTP2Graph?style=for-the-badge)](https://github.com/SMTP2Graph/SMTP2Graph/releases)
[![GitHub Last Release Date](https://img.shields.io/github/release-date/smtp2graph/smtp2graph?style=for-the-badge)](https://github.com/SMTP2Graph/SMTP2Graph/releases)
[![GitHub Sponsors](https://img.shields.io/github/sponsors/smtp2graph?style=for-the-badge&logo=githubsponsors)](https://github.com/sponsors/SMTP2Graph)
[![GitHub Repo stars](https://img.shields.io/github/stars/smtp2graph/smtp2graph?style=for-the-badge&logo=github&color=E3B341)](https://github.com/SMTP2Graph/SMTP2Graph/stargazers)
[![Docker pulls](https://img.shields.io/docker/pulls/smtp2graph/smtp2graph?style=for-the-badge&logo=docker)](https://hub.docker.com/r/smtp2graph/smtp2graph)

## Documentation

[Full documentation](https://www.smtp2graph.com) | [Installation](https://www.smtp2graph.com/#/installation)

## What is it

SMTP2Graph is an SMTP server that will send messages over the Microsoft 365/Exchange Online platform. You don't need a userlicense for this, but you need to create an application registration in Entra ID (Azure AD) and assign it the desired permissions.

## Features

- SMTP AUTH support (PLAIN and LOGIN)
- TLS support
- IP whitelist
- FROM whitelist
- Rate limiter
- Brute force protection
- No issues with SPF/DKIM/DMARC (it's handled by M365)

## Support the project

If you like this project, please consider supporting its development.

[![GitHub Sponsors](https://img.shields.io/badge/Sponsor-Github?style=for-the-badge&logo=githubsponsors&label=GitHub)](https://github.com/sponsors/SMTP2Graph)
[![Paypal donation](https://img.shields.io/badge/Donate-2997D8?style=for-the-badge&logo=paypal&label=Paypal)](https://paypal.me/roelvbdev)

## Docker usage (non-root)

The official image is based on node:alpine and runs as a non-root user (node). By default it listens on the unprivileged SMTP submission port 2587 inside the container and uses `/data` as its working directory.

- Container listen port (default): 2587 (override with `--receive.port` or `receive.port` in config)
- Volume: mount your config and any data to `/data` (ensure it is writable by UID/GID of the node user inside the container)

Examples (choose one port per container instance)

- Submission (587 on host mapped to 2587 in container):

 ```sh
 docker run --rm -p 587:2587 -v $(pwd):/data smtp2graph/smtp2graph:latest
 ```

- SMTP (25 on host mapped to 2525 in container):

 ```sh
 docker run --rm -p 25:2525 -v $(pwd):/data smtp2graph/smtp2graph:latest
 ```

- SMTPS (465 on host mapped to 2465 in container; requires secure config):

 ```sh
 docker run --rm -p 465:2465 -v $(pwd):/data smtp2graph/smtp2graph:latest
 ```

docker-compose snippet (submission example):

```yaml
services:
 smtp2graph:
  image: smtp2graph/smtp2graph:latest
  ports:
   - "587:2587"
  volumes:
   - ./data:/data
```

Note: Configure the in-container port via your config file (`receive.port`) or by leaving it undefined to use the image default (2587). If you set `receive.port` in your config, that value will be used.

Binding privileged ports inside the container

Running as non-root cannot bind ports <1024. If you must bind privileged ports from within the container, you can grant only the needed capability or lower the unprivileged port threshold:

- Add capability for binding low ports:

 ```sh
 docker run --rm --cap-add=NET_BIND_SERVICE -p 25:25 -v $(pwd):/data smtp2graph/smtp2graph:latest
 ```

- Allow unprivileged processes to bind low ports (kernel-wide in the container):

 ```sh
 docker run --rm --sysctl net.ipv4.ip_unprivileged_port_start=0 -p 25:25 -v $(pwd):/data smtp2graph/smtp2graph:latest
 ```

Volumes and permissions

The image uses the `node` user. Ensure bind-mounted directories are writable by this user. If needed, adjust permissions on the host, for example:

```sh
sudo chown -R $(id -u):$(id -g) ./data
```

Alternatively, you can mount with appropriate uid/gid mapping or use named volumes managed by Docker.
