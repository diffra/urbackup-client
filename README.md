# UrBackup Docker Client
Source files for the Docker image diffra/urbackup-client at https://hub.docker.com/r/diffra/urbackup-client/.

## Introduction
The UrBackup solution has a comprehensive list of featuers and supported systems. The official website states that 

> UrBackup is an easy to setup Open Source client/server backup system, that through a combination of image and file backups accomplishes both data safety and a fast restoration time.

This Docker image runs a UrBackup client. Note that it does not run the UrBackup server. Please see https://www.urbackup.org for more information on the complete UrBackup solution.

**This image is forked from hejare's project of the same name.** The only modifications are an update to Ubuntu server 20.04 (from 18.04) and UrBackup 2.4.11 (from 2.4.9)

## Starting the Docker container
You may start the container by running

```
docker run \
  -p 35621:35621 -p 35622:35622 -p 35623:35623 \
  -v /path/on/host:/backup/name:ro \
  diffra/urbackup-client
```

This will start the UrBackup client and automatically connect to any UrBackup servers on the local network. The necessary ports are forwarded from the Docker container. By default, the container will backup files found in ```/backup``` and all its sub directories. Mounting a host directory to that path enables the client to backup the files. Note the ```:ro``` parameter making the mount read-only for the container. 


This can easily be run using docker-compose as follows:

```
version: '2'
services:
  urbackupclient:
    container_name: urbackupclient
    hostname: urbackupclient
    image: diffra/urbackup-client
    environment:
      - COMPUTERNAME=NameToDisplayInUrBackup
    volumes:
      - /mnt/user/Vault:/backup/vault
      - /mnt/user/Media:/backup/media
    ports:
      - 35621:35621
      - 35622:35622
      - 35623:35623
```


In order to connect the client to a server outside of the local network, a number of additional parameters as environment variables are needed:

```
docker run \
  -p 35621:35621 -p 35622:35622 -p 35623:35623 \
  -v /path/on/host:/backup/name:ro \
  -e COMPUTERNAME=<your-client-computer-name> \
  -e INTERNET_MODE_ENABLED=true \
  -e INTERNET_SERVER=<your-server-name-or-ip> \
  -e INTERNET_AUTHKEY=<your-client-authkey> \
  diffra/urbackup-client
```

There is also an optional parameter ```INTERNET_SERVER_PORT``` that defaults to 55415.
