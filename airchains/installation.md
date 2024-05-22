---
cover: ../.gitbook/assets/Airchains_banner.png
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
Memory: 8GB RAM
Storage: 200Gb SSD Storage
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
VER="1.21.6"
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
echo "export AIRCHAIN_CHAIN_ID="junction"" >> $HOME/.bash_profile
echo "export AIRCHAIN_PORT="19"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```

## 4. **Download Binary:**

```
cd $HOME
wget -O junctiond https://github.com/airchains-network/junction/releases/download/v0.1.0/junctiond
chmod +x junctiond
mv junctiond $HOME/go/bin/
```

## 5. **Config and init app:**

```
junctiond init $MONIKER --chain-id $AIRCHAIN_CHAIN_ID 
sed -i -e "s|^node *=.*|node = \"tcp://localhost:${AIRCHAIN_PORT}657\"|" $HOME/.junction/config/client.toml
```

## 6. **Download genesis and addrbook:**

```
wget -O $HOME/.junction/config/genesis.json https://testnet-files.itrocket.net/airchains/genesis.json
wget -O $HOME/.junction/config/addrbook.json https://testnet-files.itrocket.net/airchains/addrbook.json
```

## 7. Set Seed and Peers:

```
SEEDS="04e2fdd6ec8f23729f24245171eaceae5219aa91@airchains-testnet-seed.itrocket.net:19656"
PEERS="47f61921b54a652ca5241e2a7fc4ed8663091e89@airchains-testnet-peer.itrocket.net:19656,e78a440c57576f3743e6aa9db00438462980927e@5.161.199.115:26656,d9a5e20668955bdd5c2fc28cffd6f06e23794638@[2a01:4f8:10a:3a51::2]:43456,61e609631e8be6e04c43ed3d7bfef23c064dc912@[2a01:4f8:221:26e0::2]:43456,f786dcc80601ddd33ba98c609795083ba418d740@158.220.119.11:43456,0b1159b05e940a611b275fe0006070439e5b6e69@[2a03:cfc0:8000:13::b910:277f]:13756,c8f6b1a795a6d9cd2ec39faf277163a9711fc81b@38.242.194.19:43456,ddd9aade8e12d72cc874263c8b854e579903d21c@178.18.240.65:26656,552d2a5c3d9889444f123d740a20237c89711109@109.199.96.143:43456,cc27f4e54a78b950adaf46e5413f92f5d53d2212@209.126.86.186:43456,f5b69a02abeb3340ccd266f049ed6aabc7c0ea88@94.72.114.150:43456"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.junction/config/config.toml
```

## 8. Set custom ports in app.toml:

```
sed -i.bak -e "s%:1317%:${AIRCHAIN_PORT}317%g;
s%:8080%:${AIRCHAIN_PORT}080%g;
s%:9090%:${AIRCHAIN_PORT}090%g;
s%:9091%:${AIRCHAIN_PORT}091%g;
s%:8545%:${AIRCHAIN_PORT}545%g;
s%:8546%:${AIRCHAIN_PORT}546%g;
s%:6065%:${AIRCHAIN_PORT}065%g" $HOME/.junction/config/app.toml
```

## 9. Set custom ports in config.toml file:

```
sed -i.bak -e "s%:26658%:${AIRCHAIN_PORT}658%g;
s%:26657%:${AIRCHAIN_PORT}657%g;
s%:6060%:${AIRCHAIN_PORT}060%g;
s%:26656%:${AIRCHAIN_PORT}656%g;
s%^external_address = \"\"%external_address = \"$(wget -qO- eth0.me):${AIRCHAIN_PORT}656\"%;
s%:26660%:${AIRCHAIN_PORT}660%g" $HOME/.junction/config/config.toml
```

## 10. Config Pruning:

```
sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.junction/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.junction/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"50\"/" $HOME/.junction/config/app.toml
```

## 11. Set minium gas price, enable prometheus and disable indexing:

```
sed -i 's|minimum-gas-prices =.*|minimum-gas-prices = "0.001amf"|g' $HOME/.junction/config/app.toml
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.junction/config/config.toml
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.junction/config/config.toml
```

## 12. Create Service file

```
sudo tee /etc/systemd/system/junctiond.service > /dev/null <<EOF
[Unit]
Description=Airchains node
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/.junction
ExecStart=$(which junctiond) start --home $HOME/.junction
Restart=on-failure
RestartSec=5
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```

## 13. Reset and Download snapshot:

```
junctiond tendermint unsafe-reset-all --home $HOME/.junction
if curl -s --head curl https://testnet-files.itrocket.net/airchains/snap_airchains.tar.lz4 | head -n 1 | grep "200" > /dev/null; then
  curl https://testnet-files.itrocket.net/airchains/snap_airchains.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.junction
    else
  echo no have snap
fi
```

## 14. Enable and Start service:

```
sudo systemctl daemon-reload
sudo systemctl enable junctiond
sudo systemctl restart junctiond && sudo journalctl -u junctiond -f
```

## 15. Create Wallet:

```
junctiond keys add $WALLET
```

## 16. Check Balance:

```
junctiond query bank balances $WALLET_ADDRESS
```

## 17. Check sync status:

```
junctiond status 2>&1 | jq
```

## 18. Create Validator:

```
cd $HOME
# Create validator.json file
echo "{\"pubkey\":{\"@type\":\"/cosmos.crypto.ed25519.PubKey\",\"key\":\"$(junctiond comet show-validator | grep -Po '\"key\":\s*\"\K[^"]*')\"},
    \"amount\": \"1000000amf\",
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
junctiond tx staking create-validator validator.json \
    --from $WALLET \
    --chain-id junction \
    --fees 200amf \
```

