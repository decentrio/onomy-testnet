# onomy-testnet-3

## Instructions
As the chain is alrady running, validators don't need to submit gentx but use the faucet to get token to create validator later.

- Version: `v1.1.6-remove-onex`
- Genesis: https://raw.githubusercontent.com/decentrio/onomy-testnet/refs/heads/main/onomy-testnet-3/genesis.json
- RPC: https://onomy-testnet.rpc.decentrio.ventures:443
- API: https://onomy-testnet.api.decentrio.ventures:443
- gRPC: https://onomy-testnet.grpc.decentrio.ventures:443:9090
- Our peer: `9a4f3a2ed6248050148115da1871a813b0de4456@65.109.145.247:2000`

You can run this script to automate the setup:
```bash
#!/bin/bash
P2P=$1
RPC=$2
gRPC=$3
gRPC_WEB=$4
MONIKER=$5

cd $HOME/go/bin
wget -O onomyd https://github.com/onomyprotocol/onomy/releases/download/v1.1.6-remove-onex/onomyd
onomyd init $MONIKER
cd .onomy
wget -O config/genesis.json https://raw.githubusercontent.com/decentrio/onomy-testnet/refs/heads/main/onomy-testnet-3/genesis.json
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"9a4f3a2ed6248050148115da1871a813b0de4456@65.109.145.247:2000\"/" config/config.toml
sed -i 's#"tcp://127.0.0.1:26657"#"tcp://0.0.0.0:'"${RPC}"'"#g' config/config.toml
sed -i 's#"tcp://0.0.0.0:26656"#"tcp://0.0.0.0:'"${P2P}"'"#g' config/config.toml
sed -i 's/indexer = "kv"/indexer = "null"/g' config/config.toml
sed -i 's/pruning = "default"/pruning = "everything"/g' config/app.toml
sed -i 's#"0.0.0.0:9090"#"0.0.0.0:'"${gRPC}"'"#g' config/app.toml
sed -i 's#"0.0.0.0:9091"#"0.0.0.0:'"${gRPC_WEB}"'"#g' config/app.toml
```

Then you can start the node:
```bash
onomyd start
```

To get faucet, you can go to channel (#faucet)[] on Discord to request. Each request will give you 100NOM every 24h
```
$request  <your-address>
```

The you can create the validator:
```bash
onomyd tx staking create-validator --amount 100000000000000000000anom --from <key> --pubkey $(onomyd tendermint show-validator) --commission-rate 0.05 --commission-max-rate 0.2 --commission-max-change-rate 0.01 --node https://onomy-testnet.rpc.decentrio.ventures:443 --min-self-delegation 10000000000000000000 --chain-id onomy-testnet-3
```
