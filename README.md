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
| VPN_USERNAME | `username`                  | none                         | Gluetun control server username                                 |
| VPN_PASSWORD | `password`                  | none                         | Gluetun control server password or api key                      |
| VPN_ADDR     | `http://192.168.1.100:8000` | `http://localhost:8000`      | HTTP URL for the VPN container control server, with port        |

### Volumes

| Host location   | Container location | Mode | Description                                               |
|-----------------|--------------------|------|-----------------------------------------------------------|
| `/my/host/dir/` | `/config`          | `ro` | The directory in which the forwared ports file is located |

### Docker Compose

## With Port File
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

## With Gluetun API
```yaml
services:
  qbittorrent-port-forwarder:
    container_name: qbittorrent-port-forwarder
    image: ghcr.io/claabs/qbittorrent-port-forward-file:latest
    environment:
      - QBT_USERNAME=admin
      - QBT_PASSWORD=adminadmin
      - QBT_ADDR=http://192.168.1.100:8080
      - VPN_USERNAME=admin
      - VPN_PASSWORD=adminadmin
      - VPN_ADDR=http://192.168.1.100:8000
    restart: unless-stopped
```

## Context

For use with [docker-wireguard-pia](https://github.com/thrnz/docker-wireguard-pia) (although you should use [`PORT_SCRIPT`](https://github.com/thrnz/docker-wireguard-pia/issues/26#issuecomment-868165281)):

* Environment variable: `PORT_FORWARDING=1`
* Environment variable: `PORT_PERSIST=1`
* Environment variable: `PORT_FILE=/pia/forwarded_port.txt`
* Volume: `/my/host/dir:/pia:rw`

For use with [gluetun](https://github.com/qdm12/gluetun) port file:

* Environment variable: `PRIVATE_INTERNET_ACCESS_VPN_PORT_FORWARDING=on`
* Environment variable: `PRIVATE_INTERNET_ACCESS_VPN_PORT_FORWARDING_STATUS_FILE=/gluetun/forwarded_port.txt`
* Volume: `/my/host/dir:/gluetun:rw`

For use with [gluetun](https://github.com/qdm12/gluetun) API:

* Environment variable: `PRIVATE_INTERNET_ACCESS_VPN_PORT_FORWARDING=on`
* Environment variable: `HTTP_CONTROL_SERVER_AUTH_DEFAULT_ROLE='{"auth":"basic","username":"admin","password":"adminadmin"}'`

## Development

### Build Image

`docker build . -t qbittorrent-port-forwarder`

### Run Container

`docker run --rm -it -e QBT_ADDR=http://192.168.1.100:8080 -v $(pwd)/config:/config qbittorrent-port-forwarder:latest`
