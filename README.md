# qbittorrent-port-forward-file

A shell script and Docker container for automatically setting qBittorrent's listening port from a text file.

## Config

### Environment Variables

| Variable     | Example                     | Default                      | Description                                                     |
|--------------|-----------------------------|------------------------------|-----------------------------------------------------------------|
| QBT_USERNAME | `username`                  | `admin`                      | qBittorrent username                                            |
| QBT_PASSWORD | `password`                  | `adminadmin`                 | qBittorrent password                                            |
| QBT_ADDR     | `http://192.168.1.100:8080` | `http://localhost:8080`      | HTTP URL for the qBittorrent web UI, with port                  |
| PORT_FILE    | `/config/my_file.txt`       | `/config/forwarded_port.txt` | Container path to the file containing the forwarded port number |

### Volumes

| Host location   | Container location | Mode | Description                                               |
|-----------------|--------------------|------|-----------------------------------------------------------|
| `/my/host/dir/` | `/config`          | `ro` | The directory in which the forwared ports file is located |

### Docker Compose

```yaml
services:
  qbittorrent-port-forwarder:
    container_name: qbittorrent-port-forwarder
    image: ghcr.io/claabs/qbittorrent-port-forward-file:latest
    environment:
      - QBT_USERNAME=admin
      - QBT_PASSWORD=adminadmin
      - QBT_ADDR=http://192.168.1.100:8080
      - PORT_FILE=/config/forwarded_port.txt
    volumes:
      - ./config:/config:ro
    restart: unless-stopped
```

## Context

Made for use with [docker-wireguard-pia](https://github.com/thrnz/docker-wireguard-pia) (although you should use [`PORT_SCRIPT`](https://github.com/thrnz/docker-wireguard-pia/issues/26#issuecomment-868165281)):

* Environment variable: `PORT_FORWARDING=1`
* Environment variable: `PORT_PERSIST=1`
* Environment variable: `PORT_FILE=/pia/forwarded_port.txt`
* Volume: `/my/host/dir:/pia:rw`

Or for use with [gluetun](https://github.com/qdm12/gluetun):

* Environment variable: `PRIVATE_INTERNET_ACCESS_VPN_PORT_FORWARDING=on`
* Environment variable: `PRIVATE_INTERNET_ACCESS_VPN_PORT_FORWARDING_STATUS_FILE=/gluetun/forwarded_port.txt`
* Volume: `/my/host/dir:/gluetun:rw`

## Development

### Build Image

`docker build . -t qbittorrent-port-forwarder`

### Run Container

`docker run --rm -it -e QBT_ADDR=http://192.168.1.100:8080 -v $(pwd)/config:/config qbittorrent-port-forwarder:latest`
