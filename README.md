# YouTrack on Docker

This repository contains a Docker image of JetBrains [YouTrack](http://www.jetbrains.com/youtrack).

* The Docker image is available at [zhur/youtrack-docker](https://hub.docker.com/r/zhur/youtrack-docker/)
* The GitHub repository is available at [zhur/youtrack-docker](https://github.com/serg-z/youtrack-docker)

## Building and pushing image

Install Docker

<https://docs.docker.com/engine/installation/>

Create image

```bash
docker build -t zhur/youtrack-docker .
```

Login

```bash
docker login
```

Push image

```bash
docker push zhur/youtrack-docker
```

## Usage

Create a named container 'youtrack'.

```bash
docker create --name youtrack zhur/youtrack-docker
```

Start the container.
YouTrack starts and listens on port 80 in the container.
To map it to the host's port 80, use the following command to create and start the container:

```bash
docker run -t --name youtrack -p 80:80 -t zhur/youtrack-docker
```

To access container logs

```bash
docker logs -f youtrack
```

YouTrack is started and managed by [supervisor](http://supervisord.org/).

### Additional settings

YouTrack stores its data and backups at ```/opt/youtrack/data/``` and ```/opt/youtrack/backup/``` in the container.
If you wish to re-use data, it is a good idea to set up a volume mapping for these two paths. For example:

```bash
docker run -t \
 --name="youtrack" \
 -v /data/youtrack/data/:/opt/youtrack/data/ \
 -v /data/youtrack/backup/:/opt/youtrack/backup/ \
 -p 80:80 \
 zhur/youtrack-docker
```

### Service example with [jwilder/nginx-proxy](https://hub.docker.com/r/jwilder/nginx-proxy/)

```ini
[Unit]
Description=YouTrack
After=docker.service nginx-proxy.service
Requires=docker.service nginx-proxy.service

[Service]
KillMode=none
ExecStartPre=-/usr/bin/docker kill youtrack
ExecStartPre=-/usr/bin/docker rm youtrack
ExecStart=/usr/bin/docker run -t \
          --name youtrack \
          -v /data/youtrack/data/:/opt/youtrack/data/ \
          -v /data/youtrack/backup/:/opt/youtrack/backup/ \
          -e VIRTUAL_HOST=youtrack.example.com \
          zhur/youtrack-docker
ExecStop=-/usr/bin/docker stop youtrack
```
