# Docker Log drivers
[link](https://success.docker.com/article/logging-best-practices)
[top 10 docker logging gottchas](https://sematext.com/blog/top-10-docker-logging-gotchas/)
Docker supports different logging drivers used to store and/or stream container stdout and stderr logs of the main container process (pid 1). By default, Docker uses the json-file logging driver, but it can be configured to use many other drivers by setting the value of log-driver in /etc/docker/daemon.json followed by restarting the Docker daemon to reload its configuration.

To override the default container logging driver run the container with --log-driver and --log-opt options.

What about Docker Engine logs? These logs are typically handled by the default system manager logger. Most of the modern distros (CentOS 7, RHEL 7, Ubuntu 16, etc.) use systemd, which uses journald for logging and journalctl for accessing the logs. To access the Engine logs use journalctl -u docker.service.

## 'docker run' --log-driver param
options:
- awslogs
- fluentd
- gelf
- json-file
- logentries
- splunk
- etwlogs
- gcplogs
- journald
- local
- none
- syslog

## Default 'log-driver'
The default logging driver is 'json-file'. If we like to change or tune the default log-driver we must change (or create) the file: /etc/docker/daemon.json
Example of a tunning of the default log-driver (json-file)
vim /etc/docker/daemon.json
```json
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3",
    "labels": "production_status",
    "env": "os,customer",
    "compress": "true"
  }
}
```
sudo service docker restart
docker run --rm --name random-logger -d chentex/random-logger:latest
sudo ls -lah $(docker inspect --format='{{ .LogPath }}' random-logger)


## docker-compose and log-driver

```yaml
version: "3.7"
services:
  some-service:
    image: some-service
    logging:
      driver: "json-file"
      options:
        max-size: "200k"
        max-file: "10"
```

### log-driver: journald
docker run --rm --name random-logger --log-driver journald -d chentex/random-logger:latest
journalctl -u docker.service


### log-driver: syslog
docker run --rm --name random-logger --log-driver syslog --log-opt tag="{{.ImageName}}/{{.Name}}/{{.ID}}" --log-opt syslog-format=rfc5424micro -d chentex/random-logger:latest
tail -f /var/log/syslog
vim /etc/rsyslog.d/10-docker.conf
  # Log docker logs messages to file
  :msg,contains,"[DOCKERS " /var/log/my-dockers.log

## log-driver: fluentd
[docker hub link](https://hub.docker.com/r/fluent/fluentd/)

