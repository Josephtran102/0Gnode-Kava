[<img src='assets\0G-banner.png' alt='banner' width= '99.9%'>]()

<font size = 7><center><b><u>About Zero Gravity (0G)</u></b></center></font>

* ### **[Zero Gravity](https://0g.ai/)** is a pioneering data availability system featuring a decentralized storage layer designed for super scalability and security.

* ### By segregating data availability workflows into distinct publishing and storage lanes, 0G optimizes scalability through efficient partitioning while ensuring data availability through consensus-based sampling, mitigating broadcasting bottlenecks. 

* ### Its incentive-based storage mechanism fosters network participation, promoting scalability and enabling support for diverse data types across Layer 2 networks and decentralized AI platforms. network.

# System Requirements:
```
CPU : 8cores
Memory : 64GB
Storage : 1TB NVMe SSD
Bandwidth : 100mps for Download / Upload
```
# Setup & Install node:
## 1. System updates, installation of required environments:
```
sudo apt update
sudo apt install curl git jq build-essential gcc unzip wget lz4 -y
```
## 2. Install Go:
```
cd $HOME && \
ver="1.21.3" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
go version
```
## 3. Git clone:
```
git clone -b v0.1.0 https://github.com/0glabs/0g-chain.git
./0g-chain/networks/testnet/install.sh
source .profile
```
## 4.Variable settings:
Replace `your_node_name` and `wallet_name`
If you need to change the port, please also proceed with the port change
```
echo 'export MONIKER="your_node_name"' >> ~/.bash_profile
echo 'export CHAIN_ID="zgtendermint_16600-1"' >> ~/.bash_profile
echo 'export WALLET_NAME="wallet_name"' >> ~/.bash_profile
echo 'export RPC_PORT="26657"' >> ~/.bash_profile
source $HOME/.bash_profile
```
## 5. Node init:
```
cd $HOME
0gchaind config chain-id $CHAIN_ID
0gchaind init $MONIKER --chain-id $CHAIN_ID
0gchaind config node tcp://localhost:$RPC_PORT
0gchaind config keyring-backend os
```
## 6. Download genesis.json file:
```
wget -P ~/.0gchain/config https://github.com/0glabs/0g-chain/releases/download/v0.1.0/genesis.json
```
## 7. PEERS, SEED setting:
```
PEERS="c4d619f6088cb0b24b4ab43a0510bf9251ab5d7f@54.241.167.190:26656,44d11d4ba92a01b520923f51632d2450984d5886@54.176.175.48:26656,f2693dd86766b5bf8fd6ab87e2e970d564d20aff@54.193.250.204:26656,f878d40c538c8c23653a5b70f615f8dccec6fb9f@54.215.187.94:26656, 41143f378016a05e1fa4fa8aa028035a82761b48@154.12.235.67:46656" && \
SEEDS="c4d619f6088cb0b24b4ab43a0510bf9251ab5d7f@54.241.167.190:26656,44d11d4ba92a01b520923f51632d2450984d5886@54.176.175.48:26656,f2693dd86766b5bf8fd6ab87e2e970d564d20aff@54.193.250.204:26656,f878d40c538c8c23653a5b70f615f8dccec6fb9f@54.215.187.94:26656, 41143f378016a05e1fa4fa8aa028035a82761b48@154.12.235.67:46656" && \
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.0gchain/config/config.toml
```
## 8. Config setting (If you need settings, please proceed after setting them):
```
EXTERNAL_IP=$(wget -qO- eth0.me) \
PROXY_APP_PORT=26658 \
P2P_PORT=26656 \
PPROF_PORT=6060 \
API_PORT=1317 \
GRPC_PORT=9090 \
GRPC_WEB_PORT=9091
```
## 9. Port, pruning, gas price, indexer (at once):
```
sed -i \
    -e "s/\(proxy_app = \"tcp:\/\/\)\([^:]*\):\([0-9]*\).*/\1\2:$PROXY_APP_PORT\"/" \
    -e "s/\(laddr = \"tcp:\/\/\)\([^:]*\):\([0-9]*\).*/\1\2:$RPC_PORT\"/" \
    -e "s/\(pprof_laddr = \"\)\([^:]*\):\([0-9]*\).*/\1localhost:$PPROF_PORT\"/" \
    -e "/\[p2p\]/,/^\[/{s/\(laddr = \"tcp:\/\/\)\([^:]*\):\([0-9]*\).*/\1\2:$P2P_PORT\"/}" \
    -e "/\[p2p\]/,/^\[/{s/\(external_address = \"\)\([^:]*\):\([0-9]*\).*/\1${EXTERNAL_IP}:$P2P_PORT\"/; t; s/\(external_address = \"\).*/\1${EXTERNAL_IP}:$P2P_PORT\"/}" \
    $HOME/.0gchain/config/config.toml
```
```
sed -i \
    -e "/\[api\]/,/^\[/{s/\(address = \"tcp:\/\/\)\([^:]*\):\([0-9]*\)\(\".*\)/\1\2:$API_PORT\4/}" \
    -e "/\[grpc\]/,/^\[/{s/\(address = \"\)\([^:]*\):\([0-9]*\)\(\".*\)/\1\2:$GRPC_PORT\4/}" \
    -e "/\[grpc-web\]/,/^\[/{s/\(address = \"\)\([^:]*\):\([0-9]*\)\(\".*\)/\1\2:$GRPC_WEB_PORT\4/}" $HOME/.0gchain/config/app.toml
```
```
sed -i.bak -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.0gchain/config/app.toml
sed -i.bak -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.0gchain/config/app.toml
sed -i.bak -e "s/^pruning-interval *=.*/pruning-interval = \"10\"/" $HOME/.0gchain/config/app.toml
```
```
sed -i "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0ua0gi\"/" $HOME/.0gchain/config/app.toml
```
```
sed -i "s/^indexer *=.*/indexer = \"kv\"/" $HOME/.0gchain/config/config.toml
```
## 10. Create service file (at once):
```
sudo tee /etc/systemd/system/ogd.service > /dev/null <<EOF
[Unit]
Description=OG Node
After=network.target

[Service]
User=$USER
Type=simple
ExecStart=$(which 0gchaind) start --home $HOME/.0gchain
Restart=10
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
## 11. Service execution:
```
sudo systemctl daemon-reload && \
sudo systemctl enable ogd && \
sudo systemctl restart ogd
```
## 12. Download snapshot, Update Addrbook, Update Peers:
### Update addrbook:
```
curl -Ls https://snapshots.liveraven.net/snapshots/testnet/zero-gravity/addrbook.json > $HOME/.0gchain/config/addrbook.json
```
### Update Peers:
```
PEERS=$(curl -s --max-time 3 --retry 2 --retry-connrefused "https://snapshots.liveraven.net/snapshots/testnet/zero-gravity/peers.txt")
if [ -z "$PEERS" ]; then
    echo "No peers were retrieved from the URL."
else
    echo -e "\nPEERS: "$PEERS""
    sed -i "s/^persistent_peers *=.*/persistent_peers = "$PEERS"/" "$HOME/.0gchain/config/config.toml"
    echo -e "\nConfiguration file updated successfully.\n"
fi
```
### Download snapshot:
```
sudo systemctl stop ogd
cp $HOME/.0gchain/data/priv_validator_state.json $HOME/.0gchain/priv_validator_state.json.backup
rm -rf $HOME/.0gchain/data
curl -L http://snapshots.liveraven.net/snapshots/testnet/zero-gravity/zgtendermint_16600-1_latest.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.0gchain
mv $HOME/.0gchain/priv_validator_state.json.backup $HOME/.0gchain/data/priv_validator_state.json
sudo systemctl restart ogd && sudo journalctl -u ogd -f -o cat
```
You will have to wait a while until it syncs
## 13. check Latest Block Height:
```
0gchaind status | jq '{ latest_block_height: .sync_info.latest_block_height, catching_up: .sync_info.catching_up }'
```
You can see your node's latsest_block_height like below
(Or you can check `catching_up` is `false`)
If the block height is set, you can create validator.

## 14. Wallet config, check EVM address:
### Add wallet:
Replace your `wallet_name`
```
0gchaind keys add "wallet_name" --eth
```
*Important write this mnemonic phrase in a safe place*

### Export to evm address:
```
0gchaind debug addr $(0gchaind keys show "wallet_name" -a) | grep 'Address (hex):' | awk -F ': ' '{print "0x" $2}'
```
### Export private key to MetaMask
```
0gchaind keys unsafe-export-eth-key "wallet_name"
```
**Important keep your private key carefully to import Metamask*

### Faucet token:
- Faucet link: https://faucet.0g.ai/

## 15. Check Balance & Create Validator & Check Validator:
### Check balance:
```
0gchaind q bank balances $(0gchaind keys show "wallet_name" -a)
```
- Example:
```
0gchaind q bank balances $(0gchaind keys show josephtran -a)
```
*Result:*
```
balances:
- amount: "9000000"
  denom: ua0gi
pagination:
  next_key: null
  total: "0"
```
### Create Validator:
Replace:
- Node name: "Your_node_name"
- Wallet: "wallet_name"
- Detail: "0G to the moon"

```
  0gchaind tx staking create-validator \
  --amount=1000000ua0gi \
  --pubkey=$(0gchaind tendermint show-validator) \
  --moniker="Your_node_name" \
  --chain-id=zgtendermint_16600-1 \
  --commission-rate="0.10" \
  --commission-max-rate="0.20" \
  --commission-max-change-rate="0.01" \
  --details="0G to the moon" \
  --min-self-delegation="1" \
  --from="wallet_name" \
  --gas=auto \
  --gas-adjustment=1.4
```
### Edit Validator:
```
0gchaind tx staking edit-validator \
--moniker "$MONIKER" \
--chain-id=zgtendermint_16600-1 \
--identity "" \
--website "" \
--details "I love blockchain ❤️" \
--from="wallet_name" \
--gas=auto \
--gas-adjustment=1.4
```
### Check Validator:
```
0gchaind q staking validator $(0gchaind keys show "wallet_name" --bech val -a)
```

# 0G Command line:
## Delegate to your node:
- Replace `<your_wallet>` and `<amount>`
```
0gchaind tx staking delegate $(0gchaind keys show <your_wallet> --bech val -a) <amount>ua0gi \
--from <your_wallet> \
--gas=auto \
--gas-adjustment=1.4 \
-y
```



