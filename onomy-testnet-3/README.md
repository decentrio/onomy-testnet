# onomy-testnet-3
## Overview
This is new testnet `onomy-testnet-3` which includes the new `reserve` module and SDK v0.50 for testing before implementing in the mainnet. This testnet also have mainnet state at version `v2.0.0`, to test the upgrade compability.

## Instructions
As the chain is alrady running, validators don't need to submit gentx but use the faucet to get token to create validator later.

- Curent version: `v2.2.1-testnet`
- Genesis: https://raw.githubusercontent.com/decentrio/onomy-testnet/refs/heads/main/onomy-testnet-3/genesis.json
- RPC: https://onomy-testnet.rpc.decentrio.ventures:443
- API: https://onomy-testnet.api.decentrio.ventures:443
- gRPC: https://onomy-testnet.grpc.decentrio.ventures:443:9090
- Our peer: `fea1747056ba4824c61f4bdc6b78174d2615aaea@65.109.145.247:2000`

You can run this script to automate the setup:
- `install.sh` file:
    ```bash
    #!/bin/bash
    P2P=$1
    RPC=$2
    gRPC=$3
    gRPC_WEB=$4
    MONIKER=$5

    cd $HOME/go/bin
    wget -O onomyd https://github.com/onomyprotocol/onomy/releases/download/v2.2.1-testnet/onomy_2.2.1-testnet_Linux_amd64
    chmod +x onomyd
    onomyd version # should show v2.2.0-testnet

    onomyd init $MONIKER
    cd .onomy
    wget -O config/genesis.json https://raw.githubusercontent.com/decentrio/onomy-testnet/refs/heads/main/onomy-testnet-3/genesis.json
    sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"fea1747056ba4824c61f4bdc6b78174d2615aaea@65.109.145.247:2000\"/" config/config.toml
    sed -i 's#"tcp://127.0.0.1:26657"#"tcp://0.0.0.0:'"${RPC}"'"#g' config/config.toml
    sed -i 's#"tcp://0.0.0.0:26656"#"tcp://0.0.0.0:'"${P2P}"'"#g' config/config.toml
    sed -i 's/indexer = "kv"/indexer = "null"/g' config/config.toml
    sed -i 's/pruning = "default"/pruning = "everything"/g' config/app.toml
    sed -i 's#"0.0.0.0:9090"#"0.0.0.0:'"${gRPC}"'"#g' config/app.toml
    sed -i 's#"0.0.0.0:9091"#"0.0.0.0:'"${gRPC_WEB}"'"#g' config/app.toml
    ```
-  Run command: 
    ```bash
    bash install.sh 2000 2001 2002 2003 <your-moniker>
    ```

Download the snapshot:
```bash
cd $HOME/.onomy
mv data/priv_validator_state.json .
wget http://65.109.145.247:3003/onomy-testnet_126467.tar.gz
tar -xvf onomy-testnet_126467.tar.gz
mv priv_validator_state.json data
rm onomy-testnet_126467.tar.gz
```

Then you can start the node:
```bash
# skip checking invariants
onomyd start
```

To get faucet, you can go to channel [#faucet](https://discord.com/channels/790706044741222407/1291272235918819390) on Discord to request. Each request will give you these tokens every 24h:
- 1,000,000 NOM
- 100 ATOM
- 100 DAI
- 100 Axelar USDT
- 100 Axelar USDC
```
$request <your-address>
```

Then, you can create the validator. The chain runs at `v2.2.1-testnet`, which implements SDK v0.50, so the command will be a bit different. 
```bash
onomyd tx staking create-validator val.json --from <key> --node https://onomy-testnet.rpc.decentrio.ventures:443 --chain-id onomy-testnet-3
```
The `val.json` file contains validator detail and should look like this (default 100 NOM):
```json
{
    "pubkey": {"@type":"/cosmos.crypto.ed25519.PubKey","key":"pubkeyhere"},
    "amount": "99000000000000000000anom",
    "moniker": "",
    "identity": "",
    "website": "",
    "security": "",
    "details": "",
    "commission-rate": "0.05",
    "commission-max-rate": "0.2",
    "commission-max-change-rate": "0.01",
    "min-self-delegation": "1"
}
```


## Upgrades
Here is the upgrade path of the chain

| Version|Height|URL|
|----|----|---|
|v2.2.1-testnet|1|https://github.com/onomyprotocol/onomy/releases/tag/v2.2.0-testnet|

### v2.2.0-testnet
To install `v2.2.1-testnet`, follow these commands:
```bash
# stop onomy service
cd $HOME/go/bin
wget -O onomyd https://github.com/onomyprotocol/onomy/releases/download/v2.2.1-testnet/onomy_2.2.1-testnet_Linux_amd64
chmod +x onomyd
```
