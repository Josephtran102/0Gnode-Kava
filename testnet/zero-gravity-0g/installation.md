---
cover: ../../.gitbook/assets/0G-banner.png
coverY: 0
layout:
  cover:
    visible: true
    size: full
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

## Hardware requirement

CPU : 8cores\
Memory : 64GB \
Storage : 1TB NVMe SSD \
Bandwidth : 100mps for Download / Upload

## 1. System updates, installation of required environments:

```markup
sudo apt update
sudo apt install curl git jq build-essential gcc unzip wget lz4 -y
```

## 2. Install Go:

```markup
cd $HOME && \
ver="1.22.0" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> ~/.bash_profile && \
source ~/.bash_profile && \
go version
```

## 3. Git clone:

```markup
git clone -b v0.2.3 https://github.com/0glabs/0g-chain.git
cd 0g-chain
make install
0gchaind version
```

## 4. Variable settings:

{% hint style="warning" %}
Replace "_<mark style="color:purple;">your\_node\_name</mark>_", "_<mark style="color:purple;">wallet\_name</mark>_"
{% endhint %}

```markup
echo 'export MONIKER="your_node_name"' >> ~/.bash_profile
echo 'export CHAIN_ID="zgtendermint_16600-2"' >> ~/.bash_profile
echo 'export WALLET_NAME="wallet_name"' >> ~/.bash_profile
echo 'export RPC_PORT="26657"' >> ~/.bash_profile
source $HOME/.bash_profile
```

## 5. Node init:

```markup
cd $HOME
0gchaind config chain-id $CHAIN_ID
0gchaind init $MONIKER --chain-id $CHAIN_ID
0gchaind config node tcp://localhost:$RPC_PORT
0gchaind config keyring-backend os
```

## 6. Download genesis.json file:

```
rm ~/.0gchain/config/genesis.json
wget -P ~/.0gchain/config https://github.com/0glabs/0g-chain/releases/download/v0.2.3/genesis.json
```

```
0gchaind validate-genesis
```

Then verify the correctness of the genesis configuration file:



## 7. PEERS, SEED setting:

```markup
PEERS="81987895a11f6689ada254c6b57932ab7ed909b6@54.241.167.190:26656,010fb4de28667725a4fef26cdc7f9452cc34b16d@54.176.175.48:26656,e9b4bc203197b62cc7e6a80a64742e752f4210d5@54.193.250.204:26656,68b9145889e7576b652ca68d985826abd46ad660@18.166.164.232:26656" && \
SEEDS="81987895a11f6689ada254c6b57932ab7ed909b6@54.241.167.190:26656,010fb4de28667725a4fef26cdc7f9452cc34b16d@54.176.175.48:26656,e9b4bc203197b62cc7e6a80a64742e752f4210d5@54.193.250.204:26656,68b9145889e7576b652ca68d985826abd46ad660@18.166.164.232:26656" && \
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.0gchain/config/config.toml
```

## 8. Config setting (If you need settings, please proceed after setting them):

```markup
EXTERNAL_IP=$(wget -qO- eth0.me) \
PROXY_APP_PORT=26658 \
P2P_PORT=26656 \
PPROF_PORT=6060 \
API_PORT=1317 \
GRPC_PORT=9090 \
GRPC_WEB_PORT=9091
```

## 9.  Port, pruning, gas price, indexer (at once):

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
sed -i "s/^indexer *=.*/indexer = \"null\"/" $HOME/.0gchain/config/config.toml
```

## 10. Create service file (at once):// Some code

```
sudo tee /etc/systemd/system/0gd.service > /dev/null <<EOF
[Unit]
Description=0G Node
After=network.target

[Service]
User=$USER
Type=simple
ExecStart=$(which 0gchaind) start --home $HOME/.0gchain
Restart=on-failure
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

## 11. Service execution:

```markup
sudo systemctl daemon-reload && \
sudo systemctl enable 0gd && \
sudo systemctl restart 0gd && \
sudo journalctl -u 0gd -f -o cat
```

{% hint style="warning" %}
_<mark style="color:red;">\*You will have to wait a while until it syncs</mark>_
{% endhint %}

## 13. Check Latest Block Height:

```
0gchaind status | jq '{ latest_block_height: .sync_info.latest_block_height, catching_up: .sync_info.catching_up }'
```

> _You can see your node's latsest\_block\_height like below (Or you can check `catching_up` is `false`) If the block height is set, you can create validator._

## 14. Wallet config, check EVM address:

### Add wallet:

```
0gchaind keys add "wallet_name" --eth
```

> _\*Replace your `"wallet_name"`_

{% hint style="danger" %}
_\*Important: write your mnemonic phrase in a safe place_
{% endhint %}

### **Export to evm address:**

```markup
0gchaind keys unsafe-export-eth-key "wallet_name"
```

## 15.  **Faucet token:**

* Faucet link: [https://faucet.0g.ai/](https://faucet.0g.ai/)

## 16. Check Balance & Create Validator & Check Validator:

### Check balance:

```
0gchaind q bank balances $(0gchaind keys show "wallet_name" -a)
```

### Create Validator:

<pre><code>  0gchaind tx staking create-validator \
  --amount=1000000ua0gi \
  --pubkey=$(0gchaind tendermint show-validator) \
  --moniker="Your_node_name" \
  --chain-id=zgtendermint_16600-2 \
  --commission-rate="0.10" \
  --commission-max-rate="0.20" \
  --commission-max-change-rate="0.01" \
  --details="0G to the moon" \
  --min-self-delegation="1" \
  --from="wallet_name" \
  --gas=auto \
  --gas-adjustment=1.4 \
<strong>  --gas-prices 0.00252ua0gi \
</strong>  -y
</code></pre>

### Check Validator:

```
0gchaind q staking validator $(0gchaind keys show "wallet_name" --bech val -a)
```

### Edit Validator:

```
0gchaind tx staking edit-validator \
  --new-moniker "JosephTran" \
  --identity "891BC6B7C0D28458" \
  --details "Joseph Tran Validator" \
  --website "www.josephtran.xyz" \
  --security-contact "@josephtran102" \
  --chain-id "zgtendermint_16600-2" \
  --from josephtran \
  --gas-adjustment 1.5 \
  --gas auto \
  --gas-prices 0.00252ua0gi \
  -y
```

### Delegate:

```
0gchaind tx staking delegate $(0gchaind keys show <your_wallet> --bech val -a) <amount>ua0gi \
--from <your_wallet> \
--gas=auto \
--gas-adjustment=1.4 \
--gas-prices 0.00252ua0gi \
-y
```

