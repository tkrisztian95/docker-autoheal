# docker-autoheal-with-sendmail

Monitor and restart unhealthy docker containers. When restart required then send email to your inbox too.

## How to use
```bash
docker run -d \
    --name autoheal \
    --restart=always \
    -e MAIL_ENABLED=true \
    -e MAILHUB=smtp.gmail.com:587 \
    -e ROOT=youremail@gmail.com \
    -e TO=recepient@gmail.com \
    -e AUTHUSER=username@gmail.com \
    -e AUTHPASS=yourAppPass \
    -e REWRITE_DOMAIN=gmail.com \
    -e HOSTNAME=localhost \
    -e AUTOHEAL_CONTAINER_LABEL=all \
    -v /var/run/docker.sock:/var/run/docker.sock \
    tkrisztian95/autoheal-with-sendmail
```

### Autoheal
a) Apply the label `autoheal=true` to your container to have it watched.

b) Set ENV `AUTOHEAL_CONTAINER_LABEL=all` to watch all running containers.

c) Set ENV `AUTOHEAL_CONTAINER_LABEL` to existing label name that has the value `true`.

Note: You must apply `HEALTHCHECK` to your docker images first. See https://docs.docker.com/engine/reference/builder/#healthcheck for details.

### Sendmail extra

d) Set ENV `MAIL_ENABLED` to the value `true`

e) Set ENV `MAILHUB` to your smtp server (E.g.: smtp.gmail.com:587)

f) Set ENV `ROOT`

g) Set ENV `TO`

h) Set ENV `CC`

i) Set ENV `SUBJECT`

j) Set ENV `AUTHUSER`

k) Set ENV `AUTHOPASS`

Note: In case you use gmail set app password instead of your personal password. See https://support.google.com/mail/answer/185833

k) Set ENV `REWRITE_DOMAIN`

Note: See https://wiki.archlinux.org/index.php/SSMTP for ssmtp configuration details.

## ENV Defaults
```
AUTOHEAL_CONTAINER_LABEL=autoheal
AUTOHEAL_INTERVAL=5   # check every 5 seconds
AUTOHEAL_START_PERIOD=0   # wait 0 seconds before first health check
AUTOHEAL_DEFAULT_STOP_TIMEOUT=10   # Docker waits max 10 seconds (the Docker default) for a container to stop before killing during restarts (container overridable via label, see below)
DOCKER_SOCK=/var/run/docker.sock   # Unix socket for curl requests to Docker API
CURL_TIMEOUT=30     # --max-time seconds for curl requests to Docker API
MAIL_ENABLED=false
SUBJECT=[AUTOHEAL]
FROMLINEOVERRIDE=yes
TLS_CA_FILE=/etc/pki/tls/certs/ca-bundle.crt
USETLS=yes
USESTARTTLS=yes
```

### Optional Container Labels
```
autoheal.stop.timeout=20        # Per containers override for stop timeout seconds during restart
```

## Testing
```bash

docker build -t alpine-with-healthcheck ./alpine-with-healthcheck

docker run -exec -it alpine-with-healthcheck

docker build -t autoheal-with-sendmail .

docker run -d \
    -e MAIL_ENABLED=true \
    -e MAILHUB=smtp.gmail.com:587 \
    -e ROOT=youremail@gmail.com \
    -e TO=recepient@gmail.com \
    -e AUTHUSER=username@gmail.com \
    -e AUTHPASS=yourAppPass \
    -e REWRITE_DOMAIN=gmail.com \
    -e HOSTNAME=localhost \
    -e AUTOHEAL_CONTAINER_LABEL=all \
    -v /var/run/docker.sock:/var/run/docker.sock \
    autoheal-with-sendmail
```
