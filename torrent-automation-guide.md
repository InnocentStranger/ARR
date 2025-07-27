# Install Docker

```sh
sudo apt update && sudo apt upgrade -y
sudo apt install -y docker.io docker-compose
sudo usermod -aG docker $USER
```

# SSH

- On the HOST PC:

```sh
ssh-keygen -t rsa -b 4096
ssh-copy-id user@<VM_IP>
```

- On VM

```sh
sudo apt update
sudo apt install openssh-server
sudo nano /etc/ssh/sshd_config
```

# Find and change 'PasswordAuthentication' to 'no'

```sh
PasswordAuthentication no
sudo systemctl restart sshd
```

# Make Directory Structure

```sh
mkdir -p config/{jellyseerr,prowlarr,qbittorrent,radarr,sonarr,jellyfin} data/downloads/{complete,incomplete} data/media/{movies,tv} && touch docker-compose.yml
```
