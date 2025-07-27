# Install Docker

```sh
sudo apt update && sudo apt upgrade -y
sudo apt install -y docker.io docker-compose nfs-common
sudo usermod -aG docker $USER
```

# On the VM

```sh
sudo apt update
sudo apt install openssh-server
```

# Optional but highly recommended: Disable password authentication and set up key-based auth for the host to connect.

On the HOST PC:

```sh
ssh-keygen -t rsa -b 4096
ssh-copy-id user@<VM_IP>
```

# On the VM:

```sh
sudo nano /etc/ssh/sshd_config
```

# Find and change 'PasswordAuthentication' to 'no'

```sh
PasswordAuthentication no
sudo systemctl restart sshd
```

# Inside VM

```sh
mkdir -p appdata data/torrents data/media/{movies,tv}

```

# Docker-compose

```yml
services:
  prowlarr:
    image: lscr.io/linuxserver/prowlarr:latest
    container_name: prowlarr
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
    volumes: -./appdata/prowlarr:/config
    ports:
      - "9696:9696"
    restart: unless-stopped
    networks:
      - arr-net

  sonarr:
    image: lscr.io/linuxserver/sonarr:latest
    container_name: sonarr
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
    volumes: -./appdata/sonarr:/config
      -./data:/data
    ports:
      - "8989:8989"
    restart: unless-stopped
    networks:
      - arr-net

  radarr:
    image: lscr.io/linuxserver/radarr:latest
    container_name: radarr
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
    volumes: -./appdata/radarr:/config
      -./data:/data
    ports:
      - "7878:7878"
    restart: unless-stopped
    networks:
      - arr-net

  bazarr:
    image: lscr.io/linuxserver/bazarr:latest
    container_name: bazarr
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
    volumes: -./appdata/bazarr:/config
      -./data:/data # Bazarr needs to see the final media location
    ports:
      - "6767:6767"
    restart: unless-stopped
    networks:
      - arr-net

  qbittorrent:
    image: lscr.io/linuxserver/qbittorrent:latest
    container_name: qbittorrent
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
      - WEBUI_PORT=8080
    volumes: -./appdata/qbittorrent:/config
      -./data/torrents:/downloads # qBittorrent's internal download path
    ports:
      - "8080:8080"
      - "6881:6881"
      - "6881:6881/udp"
    restart: unless-stopped
    networks:
      - arr-net

  jellyseerr:
    image: fallenbagel/jellyseerr:latest
    container_name: jellyseerr
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Etc/UTC
      - LOG_LEVEL=info
    volumes: -./appdata/jellyseerr:/app/config
    ports:
      - "5055:5055"
    restart: unless-stopped
    networks:
      - arr-net

networks:
  arr-net:
    driver: bridge
```
