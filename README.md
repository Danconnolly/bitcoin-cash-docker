# bitcoin-cash-docker
Docker images for Bitcoin Cash

IN DEVELOPMENT, NOT READY YET

## Examples

Set RPC username and password through environment variables and expose RPC port.
docker run -it --rm -p 8332:8332 -e BCH_RPC_USER='daniel' -e BCH_RPC_PASSWORD='abcde' dconnolly/bitcoin-cash
You can then use bitcoin-cli from the docker host using something like bitcoin-cli -rpcuser=daniel -rpcpassword=abcde getinfo

Create a docker swarm service
docker service create --name bchunlim \
	--replicas 1 \
	--restart-max-attempts 5 \
        --network bch \
	--publish published=8332,target=8332,mode=host \
        --hostname bchunlim \
	--mount type=bind,source=~/.bchunlim,destination=/data \
	--detach=true \
	dconnolly/bitcoin-cash

