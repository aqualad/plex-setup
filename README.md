# plex-setup
Dockerized plex environment for Windows WSL that comes fully configured to run autonomously using a single command

## Running the apps

1. Start the containers to initialize the folder structure
    ```bash
    docker compose -f docker-compose.yml up --build -d # Ignore the override file used below for a mergerfs volume
    ```
1. Add your PIA credentials to the qbittorrent config and move it into the newly created config directory
   ```bash
   code ./docker/qbittorrent/config/auth.conf.example # open in editor of your choice
   cp ./docker/qbittorrent/config/auth.conf.example ~/docker/qbittorrent/config/auth.conf # copy to config dir
   docker compose build qbittorrent && docker compose restart qbittorrent # add new conf file to image and restart
   docker compose logs -f qbittorrent # verify that the auth.conf was found

## Creating a virtual disk pool
When there are multiple disks available to use for storage, Sonarr/Radarr can't dynamically select one based on free space, meaning all data will just be written to a single disk by default. The solution to this is using mergerfs to join the disks into a single virtual pool that has its own mountpoint. Mergerfs will automatically select the disk with more availability, as well as handle situations where a disk becomes full and fails when attempting to write

1. Bring down the docker containers
   ```bash
   docker compose stop
   ```
1. Install mergerfs
   ```bash
   sudo apt update && sudo apt instsall mergerfs
   ```
1. Create the directory that we'll mount into
   ```bash
   sudo mkdir /mnt/plexpool
   ```
1. Configure the new mountpoint
    ```bash
    # This example uses the D:\ and P:\ drives to create a new pool named plexpool, update the cmd to fit your disks
    sudo echo "/mnt/d:/mnt/p /mnt/plexpool mergerfs defaults,allow_other,use_ino,category.create=mfs,minfreespace=10G,moveonenospc=true 0 0" >> /etc/fstab
    ```
1. Mount the virtual disk
   ```bash
   sudo mount -a
   ```
1. Bring the containers back up
   ```bash
   docker compose up --build -d
   ```
