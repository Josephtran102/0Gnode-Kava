---
cover: ../../.gitbook/assets/AlignedLayer_banner.png
coverY: 0
layout:
  cover:
    visible: true
    size: hero
  title:
    visible: true
  description:
    visible: true
  tableOfContents:
    visible: true
  outline:
    visible: true
  pagination:
    visible: true
---

# Installation



## ⛓️ Requirements for Validators:

{% code title="Prerequisites:" %}
```markup
CPU: 4 cores
Memory: 16GB RAM
Storage: 200Gb of Storage (NVME)
Network Bandwidth: 100 Mbps
```
{% endcode %}

## 1. System updates, installation of required environments:

```
sudo apt update && sudo apt upgrade -y
sudo apt install curl git wget htop tmux build-essential jq make lz4 gcc unzip -y
```

## 2. Install Go:

```
cd $HOME
VER="1.21.3"
wget "https://golang.org/dl/go$VER.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$VER.linux-amd64.tar.gz"
rm "go$VER.linux-amd64.tar.gz"
[ ! -f ~/.bash_profile ] && touch ~/.bash_profile
echo "export PATH=$PATH:/usr/local/go/bin:~/go/bin" >> ~/.bash_profile
source $HOME/.bash_profile
[ ! -d ~/go/bin ] && mkdir -p ~/go/bin
```

## 3. Set Vars:

```
echo "export WALLET="wallet"" >> $HOME/.bash_profile
echo "export MONIKER="test"" >> $HOME/.bash_profile
echo "export ALIGNEDLAYER_CHAIN_ID="alignedlayer"" >> $HOME/.bash_profile
echo "export ALIGNEDLAYER_PORT="50"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

## 4. **Download Binary:**

```
cd $HOME
#install Rust
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y
source $HOME/.cargo/env
#install Ignite CLI
curl https://get.ignite.com/cli | bash
sudo mv ignite /usr/local/bin/
#install bin
rm -rf $HOME/aligned_layer_tendermint
git clone https://github.com/yetanotherco/aligned_layer_tendermint.git
cd $HOME/aligned_layer_tendermint
git checkout 98643167990f8a597b331ddd879e079bafb25b08
make build-linux
```

## 5. **Config and init app:**

```
alignedlayerd init $MONIKER --chain-id alignedlayer
sed -i -e "s|^node *=.*|node = \"tcp://localhost:${ALIGNEDLAYER_PORT}657\"|" $HOME/.alignedlayer/config/client.toml
```

## 6. **Download genesis and addrbook:**

```
wget -O $HOME/.alignedlayer/config/genesis.json https://testnet-files.itrocket.net/alignedlayer/genesis.json
wget -O $HOME/.alignedlayer/config/addrbook.json https://testnet-files.itrocket.net/alignedlayer/addrbook.json
```

## 7. Set Seed and Peers:

```
SEEDS="d1a8816c1c5800b352c2a1eb0e7a156bce34ae9f@alignedlayer-testnet-seed.itrocket.net:50656"
PEERS="144c2d4fbbaf54dda837bfbc88b688fb2f02c92f@alignedlayer-testnet-peer.itrocket.net:50656,2567ea5aed4bba4e3062a1072a8f1e7fb4e4497c@65.109.85.36:26656,51ca4087558ebe93a16e3f1e84a969d30e7a91f1@95.216.245.35:26656,5f0e7fbfef5865c15be8d564f4b11f56abecc501@164.68.108.76:26656,18e1adeadb8cc596375e4212288fcd00690df067@213.199.48.195:26656,c2a2bb5cf9d126b623d5ef40151cad73b5d7f4e8@37.60.248.85:26656,7292de855372480ae23dbcaf94d36ead187cf6a8@194.163.143.206:50656,a1d6d9569789a7a8765f0a4899439819f07755d4@213.133.103.213:26656,614fe5111b7d5e0713b41923e9fdc7a354ed5a7d@84.247.142.109:26656,afeea4cd47aa80504adbdaa8aa019864e291de55@[2a03:cfc0:8000:13::b910:277f]:13356,5cbed18b59353db1ed7f864d621f271cae549d68@207.180.219.100:26656"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.alignedlayer/config/config.toml
```

## 8. Set custom ports in app.toml:

```
sed -i.bak -e "s%:1317%:${ALIGNEDLAYER_PORT}317%g;
s%:8080%:${ALIGNEDLAYER_PORT}080%g;
s%:9090%:${ALIGNEDLAYER_PORT}090%g;
s%:9091%:${ALIGNEDLAYER_PORT}091%g;
s%:8545%:${ALIGNEDLAYER_PORT}545%g;
s%:8546%:${ALIGNEDLAYER_PORT}546%g;
s%:6065%:${ALIGNEDLAYER_PORT}065%g" $HOME/.alignedlayer/config/app.toml
```

## 9. Set custom ports in config.toml file:

```
sed -i.bak -e "s%:26658%:${ALIGNEDLAYER_PORT}658%g;
s%:26657%:${ALIGNEDLAYER_PORT}657%g;
s%:6060%:${ALIGNEDLAYER_PORT}060%g;
s%:26656%:${ALIGNEDLAYER_PORT}656%g;
s%^external_address = \"\"%external_address = \"$(wget -qO- eth0.me):${ALIGNEDLAYER_PORT}656\"%;
s%:26660%:${ALIGNEDLAYER_PORT}660%g" $HOME/.alignedlayer/config/config.toml
```

## 10. Config Pruning:

```
sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.alignedlayer/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.alignedlayer/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"50\"/" $HOME/.alignedlayer/config/app.toml
```

## 11. Set minium gas price, enable prometheus and disable indexing:

```
sed -i 's|minimum-gas-prices =.*|minimum-gas-prices = "0.0001stake"|g' $HOME/.alignedlayer/config/app.toml
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.alignedlayer/config/config.toml
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.alignedlayer/config/config.toml
```

## 12. Create Service file

```
sudo tee /etc/systemd/system/alignedlayerd.service > /dev/null <<EOF
[Unit]
Description=Alignedlayer node
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/.alignedlayer
ExecStart=$(which alignedlayerd) start --home $HOME/.alignedlayer
Restart=on-failure
RestartSec=5
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```

## 13. Reset and Download snapshot:

```
alignedlayerd tendermint unsafe-reset-all --home $HOME/.alignedlayer
if curl -s --head curl https://testnet-files.itrocket.net/alignedlayer/snap_alignedlayer.tar.lz4 | head -n 1 | grep "200" > /dev/null; then
  curl https://testnet-files.itrocket.net/alignedlayer/snap_alignedlayer.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.alignedlayer
    else
  echo no have snap
fi
```

## 14. Enable and Start service:

```
sudo systemctl daemon-reload
sudo systemctl enable alignedlayerd
sudo systemctl restart alignedlayerd && sudo journalctl -u alignedlayerd -f
```

## 15. Create Wallet:

```
alignedlayerd keys add $WALLET
```

## 16. Check Balance:

```
alignedlayerd query bank balances $WALLET_ADDRESS
```

## 17. Check sync status:

```
alignedlayerd status 2>&1 | jq
```

## 18. Create Validator:

```
cd $HOME
# Create validator.json file
echo "{\"pubkey\":{\"@type\":\"/cosmos.crypto.ed25519.PubKey\",\"key\":\"$(alignedlayerd comet show-validator | grep -Po '\"key\":\s*\"\K[^"]*')\"},
    \"amount\": \"1000000stake\",
    \"moniker\": \"test\",
    \"identity\": \"\",
    \"website\": \"\",
    \"security\": \"\",
    \"details\": \"I love blockchain ❤️\",
    \"commission-rate\": \"0.1\",
    \"commission-max-rate\": \"0.2\",
    \"commission-max-change-rate\": \"0.01\",
    \"min-self-delegation\": \"1\"
}" > validator.json
# Create a validator using the JSON configuration
alignedlayerd tx staking create-validator validator.json \
    --from $WALLET \
    --chain-id alignedlayer \
    --fees 50stake \
	
```

