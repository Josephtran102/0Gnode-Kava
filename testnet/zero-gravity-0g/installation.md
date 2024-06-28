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
PEERS="4c7b139e9ec5671887ba66e0f5ff8c1192cf8f43@37.27.93.29:26656,783075b160d5a1512ac2a19ee42315542eaf9ef5@113.165.23.45:12656,25d493bbf138ee5e77ba435fdefb6c015bfb2566@5.9.101.156:16656,baeceedd1ec1ba6ce1b6d19bb40f7b571026fb05@75.119.136.242:26646,cd662c11f7b4879b3861a419a06041c782f1a32d@89.116.24.249:26656,b6a05c26f8d2d18e0e77b2e991845eb43115714c@144.76.30.120:34656,27ee695cd66c6499a604199a5216f9aea5915871@138.201.217.124:26656,7dc0785d70b271ff8ea35e27ce972708ac5ec083@37.27.120.91:26656,16dbdba70d0727c2680b71194860828e4ae54c6d@207.148.64.35:26656,bed108e9ce56d84a574fa02df90c734281ae19ef@162.55.65.137:27856,701ad95fced2cdf1549c9ea1606e890ca079ff1f@65.109.51.119:26656,e6da81745db1b31d14a29943dac6556940106bae@49.12.175.10:26656,e2904d09cee5dc95125d8dee2eb028e91c3e73ac@95.217.114.220:36656,eab7a8c58d5266bef0c61855e6da0c52be13e71d@100.42.183.246:26656,0a1a4d3319eac14b05b3f96f6b23869a1d7d4341@109.199.123.151:36656,6a67f710aa672a7a9feafd08bc92ec0802806717@194.163.135.92:36656,710f94642675d82190d43d272a77dfeb1daaf940@5.9.61.237:19656,a370f91e1cb6d4e7b7b5f9544c4cadde8f5a889b@23.88.70.109:58656,7dca7a6c5e625a96916d9d9404f3abb637c9c264@135.181.239.240:26656,d7921529d985b18096ea5cc5d023806af91fd51e@157.90.128.250:58656,88a7e34ed28532d71f9ab1e958b0a63bdfdbeb70@65.21.123.172:45656,948684d503447fd379504afb5d33e5efe9a41cf4@185.209.228.12:26656,3ab056f065ee9d37c4ad2f393033d292651182ab@159.69.72.247:17656,7c49d2a9bfee4e9fb3998ffd2c16b79666b5763d@65.21.97.150:26656,07cec7e7d6ac7656af036ca3526c793bf90670a8@65.21.225.121:26656,840a4a311fc05434e519b01e2466eeabffd13dc5@213.35.120.215:26656,b52e14e2317a81421c452d32956d560249858f54@37.27.127.27:26656,5f99856a142796b2820645fd56906ee1e55bd0b4@62.169.23.220:26656" && \
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
sudo systemctl start 0gd && \
sudo journalctl -u 0gd -f -o cat
```

{% hint style="warning" %}
_<mark style="color:red;">\*You will have to wait a while until it syncs</mark>_
{% endhint %}

## 12. Check Latest Block Height:

```
0gchaind status | jq '{ latest_block_height: .sync_info.latest_block_height, catching_up: .sync_info.catching_up }'
```

> _You can see your node's latsest\_block\_height like below (Or you can check `catching_up` is `false`) If the block height is set, you can create validator._

## 13. Wallet config, check EVM address:

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

## 14.  **Faucet token:**

* Faucet link: [https://faucet.0g.ai/](https://faucet.0g.ai/)

## 15. Check Balance & Create Validator & Check Validator:

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
--chain-id=zgtendermint_16600-2 \
--gas=auto \
--gas-adjustment=1.4 \
--gas-prices 0.00252ua0gi \
-y
```

