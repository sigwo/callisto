# Running a Validator

## Add swap

For a more verbose set of instructions use [Digital Ocean's guide](https://www.digitalocean.com/community/tutorials/how-to-add-swap-space-on-ubuntu-22-04) (the original source of these commands).

> **NOTE: 2 GB may not be adequate, read about calculating the proper swap space**

`df -h`

`sudo fallocate -l 2G /swapfile`

`ls -lh /swapfile`

`sudo chmod 600 /swapfile`

`ls -lh /swapfile`

`sudo mkswap /swapfile`

`sudo swapon /swapfile`

`sudo swapon --show`

`free -h`

`sudo cp /etc/fstab /etc/fstab.bak`

`echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab`

## Install Go

`wget https://go.dev/dl/go1.18.3.linux-amd64.tar.gz`

`rm -rf /usr/local/go && tar -C /usr/local -xzf go1.18.3.linux-amd64.tar.gz`

`export PATH=$PATH:/usr/local/go/bin`

`source ~/.profile`

`go version`

## Install jq:

`sudo apt install jq`

## Clone the Callisto repository

`git clone https://github.com/sigwo/callisto.git`

## Switch to dev branch (may not be needed soon)

`cd callisto`

`git checkout dev`

## Move jupiterd binary:

`sudo mv ./jupiterd /usr/local/bin`

## Run initializer:

`./testnet-init.sh`

Let it run until you see output that blocks are being executed & committed. Then kill it with `CTRL + C`

## Move Genesis:

`cp ./testnet-genesis.json ~/.ethermintd/config/genesis.json`

## Delete Chain Data from Init:

`cd ~/.ethermintd/data`

**Warning: The next command is dangerous if you're not in the right directory!**

`rm -rf -v !("priv_validator_state.json")`

## Add a good seed to `seeds=` or `persistent_peers=` in ~/.ethermintd/config/config.toml:

```
# Comma separated list of seed nodes to connect to

seeds = ""

# Comma separated list of nodes to keep persistent connections to

persistent_peers = "ab36dc7a7d916370e16895e10b9249754d4f5708@104.131.166.158:26656"
```

## Fire up the box

`jupiterd start --pruning=nothing --evm.tracer=json --trace --log_level info --minimum-gas-prices=0.0001ajup --json-rpc.api eth,txpool,personal,net,debug,web3,miner --api.enable`
