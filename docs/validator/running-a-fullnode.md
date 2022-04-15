---
sidebar_position: 2
---

# Running a full Aura node

This is a detailed documentation for setting up a **Full node** on Aura network.

:::caution Remarks:
The following is the minimal setup to join Aura network.
If running a validator node, you should research validator security.
:::

## Prerequisites

- Go 1.17+
- GCC/G++ compiler

### Supported OS

We officially support Linux only. Other platforms may work, but there is no guarantee. We will extend our support to other platforms after we have stabilized our current architecture.

### Commonly used ports

`aurad` uses the following TCP ports. Toggle their settings to fit your environment.

- *26656*: The default port for the P2P protocol. This port is used to communicate with other nodes and must be open to join a network. However, it does not have to be open to the public. For validator nodes, we recommend configuring `persistent_peers` and closing this port to the public.

Additional ports:

- *1317*: The default port for the Lite Client Daemon (LCD), which can be executed by `aurad rest-server`. The LCD provides an HTTP RESTful API layer to allow applications and services to interact with your `aurad instance` through RPC. You don’t need to open this port unless you have use for it.

- *26657*: The default port for the `Tendermint RPC protocol`. Because this port is used for querying and sending transactions, it must be open for serving queries from `aurad`.

## Networks information

Specify [the network](networks-info.md) you want to join by choosing the corresponding genesis file and seeds 

## Setup a full-node

This guide completes the following actions:
- Compile `aurad`
- Give your node a moniker and configure it
- Configure genesis state 

```bash
$ git clone https://github.com/aura-nw/aura
$ cd aura
$ make
$ aurad init <moniker> 
$ wget <genesis-json-link>
$ mv genesis.json ~/.aura/config/genesis.json
```

Start your full-node:
```bash
$ aurad start --p2p.seeds <seed-id>@<seed-ip>:<seed-p2p-port> --minimum-gas-prices <gas-price>
```
:::note For optimized node performance, set `minimum-gas-prices` to enable the anti-spam mechanism and reject incoming transactions with less than the minimum gas prices.
:::

After starting your full-node, wait until it completely sync transactions to your local to start create your validator.

###  Optional Configuration: State Sync
State sync rapidly bootstraps a new node by discovering, fetching, and restoring a state machine snapshot from peers instead of fetching and replaying historical blocks

Visit a explorer to get a recent block height and corresponding hash. The recommended snapshot period is 1000 blocks, it is advised to choose something close to current height - 1000. Set these parameters in the code snippet below <BLOCK_HEIGHT>, <BLOCK_HASH>, <RPC_SERVER_1> and <RPC_SERVER_2>

```
$ cd $HOME/.aura/config
$ sed -i 's/enable = false/enable = true/' config.toml
$ sed -i 's/trust_height = 0/trust_height = <BLOCK_HEIGHT>/' config.toml
$ sed -i 's/trust_hash = ""/trust_hash = "<BLOCK_HASH>"/' config.toml
$ sed -i 's/rpc_servers = ""/rpc_servers = "<RPC_SERVER_1>,<RPC_SERVER_2>"/' config.toml
```