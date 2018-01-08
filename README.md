# bitcoin-cash-docker
Docker images for Bitcoin Cash, see https://hub.docker.com/r/dconnolly/bitcoin-cash/

## Versions
* latest, unlimited, unlimited-1.2.0.0 - [Bitcoin Unlimited Cash v1.2.0.0](https://github.com/Danconnolly/bitcoin-cash-docker/blob/master/unlimited/1.2.0.0/Dockerfile)
* unlimited-1.1.2.0 - [Bitcoin Unlimited Cash v1.1.2.0](https://github.com/Danconnolly/bitcoin-cash-docker/blob/master/unlimited/1.1.2.0/Dockerfile)
* abc, abc-0.16.2 - [Bitcoin ABC v0.16.2](https://github.com/Danconnolly/bitcoin-cash-docker/blob/master/abc/0.16.2/Dockerfile)
*  abc-0.16.1 - [Bitcoin ABC v0.16.1](https://github.com/Danconnolly/bitcoin-cash-docker/blob/master/abc/0.16.1/Dockerfile)

## Quick Start

* simple Unlimited node: `docker run -it --name bchunlim dconnolly/bitcoin-cash:latest`
* simple ABC node: `docker run -it --name bchabc dconnolly/bitcoin-cash:abc`

To use `bitcoin-cli` you can use something like
````
docker exec --user bitcoin $(docker ps --filter name=bchunlim -q) bitcoin-cli -conf=/home/bitcoin/bitcoin.conf getinfo
````

## Data 
Bitcoin Cash data is stored in the container at `/data`. To preserve the data, mount
a volume at this location using `-v host_dir:/data`.

## Configuration
You can include your own `bitcoin.conf` in the `/data` volume.

Some notes:
* `printtoconsole=1` - causes output to be sent to the standard output
* `rpcallowip=::/0` - enables RPC port on docker internal network interface
* do not set `daemon=1` otherwise the container will exit immediately
* do not include RPC usernames/passwords, see below

## RPC Authentication
RPC username and password can be set using either Docker secrets or environment variables. 
Secrets take precendence.

Environment variables are `BCH_RPC_USER` and `BCH_RPC_PASSWORD`.

Docker secrets are `bchrpcuser` and `bchrpcpassword`. The secrets must be just the value
(e.g. `echo "rpcusername" | docker secret create bchrpcuser`). Secrets are useful if other
services need to use RPC.

## Examples
Set RPC username and password through environment variables, expose RPC port:
````
docker run -it --rm -p 8332:8332 -e BCH_RPC_USER='user' -e BCH_RPC_PASSWORD='abcde' dconnolly/bitcoin-cash
````
You can then use bitcoin-cli from the docker host using something like `bitcoin-cli -rpcuser=user -rpcpassword=abcde getinfo`

Create a docker swarm service:
````
docker service create --name bchunlim \
	--replicas 1 \
	--restart-max-attempts 5 \
	--publish published=8333,target=8333,mode=host \
	--mount type=bind,source=/var/local/bchunlim,destination=/data \
	--secret bchrpcuser \
	--secret bchrpcpassword \
	--detach=true \
	dconnolly/bitcoin-cash
````

Docker stack using a compose file. Deploy using `docker stack deploy -c docker-compose.yml`:
````
version: "3.5"

services:
  bchunlim:
    image: dconnolly/bitcoin-cash:latest
    volumes:
      - bch-data:/data
    networks:
      - bch
    ports:
      - target: 8333
        published: 8333
        protocol: tcp
        mode: host
    configs:
      - source: bchconf
        target: /data/bitcoin.conf
    secrets:
      - bchrpcuser
      - bchrpcpassword
    hostname: bchunlim
    deploy:
      replicas: 1
      restart_policy:
        condition: on-failure
        delay: 5s
        max_attempts: 3
        window: 300s

networks:
  bch:
    driver: overlay

volumes:
  bch-data:

secrets:
  bchrpcuser:
    external: true
  bchrpcpassword:
    external: true

configs:
  bchconf:
    file: ./bitcoin.conf
````

