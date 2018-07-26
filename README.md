# Pritunl as a Docker container

[Docker Hub Releases](https://hub.docker.com/r/jippi/pritunl/)

## Config (env)

- `PRITUNL_DONT_WRITE_CONFIG` if set, `/etc/pritunl.conf` will not be auto-written on container start.
- `PRITUNL_DEBUG` must be `true` or `false` - controls the `debug` config key.
- `PRITUNL_BIND_ADDR` must be a valid IP on the host - defaults to `0.0.0.0` - controls the `bind_addr` config key.
- `PRITUNL_MONGODB_URI` URI to mongodb instance, default is starting a local mongodb instance in the container and use that.

## Usage

You need to host mongodb somewhere you'd like to use for this rather than starting the built-in one you can
do so through the `PRITUNL_MONGODB_URI` env var like this:

#Build docker image

```sh
docker build -t pritunl .

docker run \
    -d \
    --privileged \
    -e PRITUNL_MONGODB_URI=mongodb://some-mongo-host:27017/pritunl \
    -p 1194:1194/udp \
    -p 1194:1194/tcp \
    -p 80:80/tcp \
    -p 443:443/tcp \
    pritunl
```

Then you can login to your pritunl web ui at https://docker-host-address

Username: pritunl Password: pritunl

I would suggest using docker data volume for persistent storage of pritunl data, something like this:

```sh
## create the data volume
docker run \
    -v /var/lib/pritunl \
    --name=pritunl-data busybox
    
## use the data volume when starting pritunl
docker run \
    --name pritunl \
    --privileged \
    --volumes-from=pritunl-data \
    -e PRITUNL_MONGODB_URI=mongodb://some-mongo-host:27017/pritunl \
    -p 1194:1194/udp \
    -p 1194:1194/tcp \
    -p 80:80/tcp \
    -p 443:443/tcp \
    pritunl
```

Then you're on your own, but take a look at http://pritunl.com or https://github.com/pritunl/pritunl

Based on `johnae/pritunl`
