# plex-setup
Dockerized plex environment for Windows WSL that comes fully configured to run autonomously using a single command

## Setup
1. Start the containers to initialize the folder structure
    ```bash
    docker compose up --build -d
    ```
1. Add your PIA credentials to the qbittorrent config and move it into the newly created config directory
   ```bash
   code ./docker/qbittorrent/config/auth.conf.example # open in editor of your choice
   cp ./docker/qbittorrent/config/auth.conf.example ~/docker/qbittorrent/config/auth.conf # copy to config dir
   docker compose build qbittorrent && docker compose restart qbittorrent # add new conf file to image and restart
   docker compose logs -f qbittorrent # verify that the auth.conf was found
