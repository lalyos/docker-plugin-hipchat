This is a [docker-plugin](https://github.com/progrium/docker-plugins) aims
to send HipChat room message at docker events: start/stop/create/destroy ...

## Authentication

Get your [personal API token](https://sequenceiq.hipchat.com/account/api), and
set it as HIPCHAT_TOKEN

```
export HIPCHAT_TOKEN
export HIPCHAT_ROOM_ID=890141
```

## Room id

list hipchat room ids
```
curl "https://api.hipchat.com/v2/room?auth_token=$HIPCHAT_TOKEN"|jq '.items[]|[.id,.name]' -c
```

## Message format

Right now the message starts with the name of the event, than a minimal
information is displayed about the container:

- name
- image
- hostname

```
EVENT {"name":"mydb","img":"posgresql","host":"boot2docker"}
```

## Customization

The message can be customized by the `HIPCHAT_JQ_PATTERN` env variable.
```
HIPCHAT_JQ_PATTERN="{name:.Name, img:.Config.Image, host:.Config.Hostname}"
```


## Standalone esting

```
alias d='export DEBUG=1'
alias ud='unset DEBUG'

export PLUGIN_PATH=/tmp/plugins
plugn init
plugn install https://github.com/lalyos/docker-plugin-hipchat.git
plugn enable docker-plugin-hipchat
plugn list

echo kaka|plugn trigger create
dockerhook -d -s "/Users/lalyos/prj/go/bin/plugn trigger"  
```

seeing plugin configs
```
plugn config export docker-plugin-hipchat
```

## Docker testing

Installing and enabling the hipchat plugin
```
docker run -it --rm \
  -e DEBUG=1 \
  -e INSTALL=https://github.com/lalyos/docker-plugin-hipchat.git \
  -e "ENABLE=docker-plugin-hipchat" \
  -v /var/run/docker.sock:/var/run/docker.sock \
  progrium/plugins /start list
```

Using `--volume` instead of installing with git
```
docker run -it --rm \
  -e DEBUG=1 \
  -e "ENABLE=docker-plugin-hipchat" \
  -v /Users/myself/plugins:/plugins \
  -v /var/run/docker.sock:/var/run/docker.sock \
  progrium/plugins /start list
```
