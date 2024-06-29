---
cover: ../../.gitbook/assets/Initia_banner.png
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
Storage: 1 TB SSD Storage
Network Bandwidth: 100 Mbps
Initiad: v0.2.10
```
{% endcode %}

> 📌 _It is recommended to use Linux OS to run Initia nodes. Running Initia node has not been tested on other OS environments, and same environment settings and running conditions cannot be guaranteed._

## 1. System updates, installation of required environments:

```
sudo apt update && \
sudo apt install curl git jq build-essential gcc unzip wget lz4 -y
```

## 2. Install Go:

```
cd $HOME && \
ver="1.22.0" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
go version
```

## 3. Install `initia` binary:

```
git clone https://github.com/initia-labs/initia.git
cd initia
git checkout v0.2.15
make install
initiad version
```

## 4. Set up variables**:**

```
# Customize if you need
echo 'export MONIKER="My_Node"' >> ~/.bash_profile
echo 'export CHAIN_ID="initiation-1"' >> ~/.bash_profile
echo 'export WALLET_NAME="wallet"' >> ~/.bash_profile
echo 'export RPC_PORT="26657"' >> ~/.bash_profile
source $HOME/.bash_profile
```

## 5. **Initialize the node:**

```
cd $HOME
initiad init $MONIKER --chain-id $CHAIN_ID
initiad config set client chain-id $CHAIN_ID
initiad config set client node tcp://localhost:$RPC_PORT
initiad config set client keyring-backend os # You can set it to "test" so you will not be asked for a password
```

## 6. Download genesis.json**:**

```
wget https://initia.s3.ap-southeast-1.amazonaws.com/initiation-1/genesis.json -O $HOME/.initia/config/genesis.json
```

## 7. Add seeds and peers to the config.toml:

```
PEERS="e3ac92ce5b790c76ce07c5fa3b257d83a517f2f6@178.18.251.146:30656,2692225700832eb9b46c7b3fc6e4dea2ec044a78@34.126.156.141:26656,2a574706e4a1eba0e5e46733c232849778faf93b@84.247.137.184:53456,40d3f977d97d3c02bd5835070cc139f289e774da@168.119.10.134:26313,1f6633bc18eb06b6c0cab97d72c585a6d7a207bc@65.109.59.22:25756,4a988797d8d8473888640b76d7d238b86ce84a2c@23.158.24.168:26656,e3679e68616b2cd66908c460d0371ac3ed7795aa@176.34.17.102:26656,d2a8a00cd5c4431deb899bc39a057b8d8695be9e@138.201.37.195:53456,329227cf8632240914511faa9b43050a34aa863e@43.131.13.84:26656,517c8e70f2a20b8a3179a30fe6eb3ad80c407c07@37.60.231.212:26656,07632ab562028c3394ee8e78823069bfc8de7b4c@37.27.52.25:19656,028999a1696b45863ff84df12ebf2aebc5d40c2d@37.27.48.77:26656,3c44f7dbb473fee6d6e5471f22fa8d8095bd3969@185.219.142.137:53456,8db320e665dbe123af20c4a5c667a17dc146f4d0@51.75.144.149:26656,c424044f3249e73c050a7b45eb6561b52d0db456@158.220.124.183:53456,767fdcfdb0998209834b929c59a2b57d474cc496@207.148.114.112:26656,edcc2c7098c42ee348e50ac2242ff897f51405e9@65.109.34.205:36656,140c332230ac19f118e5882deaf00906a1dba467@185.219.142.119:53456,4eb031b59bd0210481390eefc656c916d47e7872@37.60.248.151:53456,ff9dbc6bb53227ef94dc75ab1ddcaeb2404e1b0b@178.170.47.171:26656,ffb9874da3e0ead65ad62ac2b569122f085c0774@149.28.134.228:26656" && \
SEEDS="2eaa272622d1ba6796100ab39f58c75d458b9dbc@34.142.181.82:26656,c28827cb96c14c905b127b92065a3fb4cd77d7f6@testnet-seeds.whispernode.com:25756" && \
sed -i \
    -e "s/^seeds *=.*/seeds = \"$SEEDS\"/" \
    -e "s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" \
    "$HOME/.initia/config/config.toml"
```

{% hint style="warning" %}
_<mark style="color:red;">\*You will have to wait a while until it syncs</mark>_
{% endhint %}

## 8. Change ports (Optional)

```
# Customize if you need
EXTERNAL_IP=$(wget -qO- eth0.me) \
PROXY_APP_PORT=26658 \
P2P_PORT=26656 \
PPROF_PORT=6060 \
API_PORT=1317 \
GRPC_PORT=9090 \
GRPC_WEB_PORT=9091
```

```
sed -i \
    -e "s/\(proxy_app = \"tcp:\/\/\)\([^:]*\):\([0-9]*\).*/\1\2:$PROXY_APP_PORT\"/" \
    -e "s/\(laddr = \"tcp:\/\/\)\([^:]*\):\([0-9]*\).*/\1\2:$RPC_PORT\"/" \
    -e "s/\(pprof_laddr = \"\)\([^:]*\):\([0-9]*\).*/\1localhost:$PPROF_PORT\"/" \
    -e "/\[p2p\]/,/^\[/{s/\(laddr = \"tcp:\/\/\)\([^:]*\):\([0-9]*\).*/\1\2:$P2P_PORT\"/}" \
    -e "/\[p2p\]/,/^\[/{s/\(external_address = \"\)\([^:]*\):\([0-9]*\).*/\1${EXTERNAL_IP}:$P2P_PORT\"/; t; s/\(external_address = \"\).*/\1${EXTERNAL_IP}:$P2P_PORT\"/}" \
    $HOME/.initia/config/config.toml
```

```
sed -i \
    -e "/\[api\]/,/^\[/{s/\(address = \"tcp:\/\/\)\([^:]*\):\([0-9]*\)\(\".*\)/\1\2:$API_PORT\4/}" \
    -e "/\[grpc\]/,/^\[/{s/\(address = \"\)\([^:]*\):\([0-9]*\)\(\".*\)/\1\2:$GRPC_PORT\4/}" \
    -e "/\[grpc-web\]/,/^\[/{s/\(address = \"\)\([^:]*\):\([0-9]*\)\(\".*\)/\1\2:$GRPC_WEB_PORT\4/}" \
    $HOME/.initia/config/app.toml
```

{% hint style="warning" %}
_<mark style="color:red;">You can see your node's latsest\_block\_height like below (Or you can check</mark> <mark style="color:red;"></mark><mark style="color:red;">`catching_up`</mark> <mark style="color:red;"></mark><mark style="color:red;">is</mark> <mark style="color:red;"></mark><mark style="color:red;">`false`</mark><mark style="color:red;">) If the block height is set, you can create validator.</mark>_
{% endhint %}

## 9. Configure prunning to save storage (Optional)

```
sed -i \
    -e "s/^pruning *=.*/pruning = \"custom\"/" \
    -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" \
    -e "s/^pruning-interval *=.*/pruning-interval = \"10\"/" \
    "$HOME/.initia/config/app.toml
```

## 10. Set min gas price

```
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.15uinit,0.01uusdc\"/" $HOME/.initia/config/app.toml
```

## 11. Disable indexer

```
sed -i "s/^indexer *=.*/indexer = \"null\"/" $HOME/.initia/config/config.toml
```

## 12. Create a service file

```
sudo tee /etc/systemd/system/initiad.service > /dev/null <<EOF
[Unit]
Description=Initia Node
After=network.target

[Service]
User=$USER
Type=simple
ExecStart=$(which initiad) start --home $HOME/.initia
Restart=on-failure
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

## 13. Start the node

```
sudo systemctl daemon-reload && \
sudo systemctl enable initiad && \
sudo systemctl restart initiad && \
sudo journalctl -u initiad -f -o cat
```

## 14. Create a wallet for your validator

```
initiad keys add $WALLET_NAME

# DO NOT FORGET TO SAVE THE SEED PHRASE
# You can add --recover flag to restore existing key instead of creating
```

## 15. Request tokens from the faucet on Discord

\-> [FAUCET](https://faucet.testnet.initia.xyz/) <-

## 16. Check wallet balance

Make sure your node is fully synced unless it won't work.

```
initiad status | jq -r .sync_info
```

```
initiad q bank balances $(initiad keys show $WALLET_NAME -a) 
```

## 17. Create a validator

```
initiad tx mstaking create-validator \
  --amount=1000000uinit \
  --pubkey=$(initiad tendermint show-validator) \
  --moniker=$MONIKER \
  --chain-id=$CHAIN_ID \
  --commission-rate=0.05 \
  --commission-max-rate=0.10 \
  --commission-max-change-rate=0.01 \
  --from=$WALLET_NAME \
  --identity="" \
  --website="" \
  --details="Initia to the moon!" \
  --gas=2000000 --fees=300000uinit \
  -y
```

_<mark style="color:red;">Do not forget to save</mark> <mark style="color:red;"></mark><mark style="color:red;">`priv_validator_key.json`</mark> <mark style="color:red;"></mark><mark style="color:red;">file located in $HOME/.initia/config/</mark>_\


## Oracle Setup:

> _First, let's create a s on the same server._

```
tmux new -s oracle
```

> _This section is only valid for the active set, but you can install it now._

### Install Oracle:

```
# Clone repository
cd $HOME
rm -rf slinky
git clone https://github.com/skip-mev/slinky.git
cd slinky
git checkout v0.4.3

# Build binaries
make build

# Move binary to local bin
mv build/slinky /usr/local/bin/
rm -rf build
```

### Run Oracle:

#### Create service:

```
sudo tee /etc/systemd/system/slinky.service > /dev/null <<EOF
[Unit]
Description=Initia Slinky Oracle
After=network-online.target

[Service]
User=$USER
ExecStart=$(which slinky) --oracle-config-path $HOME/slinky/config/core/oracle.json --market-map-endpoint 0.0.0.0:17990
Restart=on-failure
RestartSec=30
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

#### Enable and Start service:

```
sudo systemctl daemon-reload
sudo systemctl enable slinky.service
sudo systemctl start slinky.service
```

#### Validating Prices:

> _When you run the command below, you should see the prices. However, if you are not in the active set, you will not see the price here. You can stop and continue with CTRL C._

```
make run-oracle-client
```

#### Enable Oracle Vote Extension:

```
###############################################################################
###                                  Oracle                                 ###
###############################################################################
[oracle]
# Enabled indicates whether the oracle is enabled.
enabled = "true"

# Oracle Address is the URL of the out of process oracle sidecar. This is used to
# connect to the oracle sidecar when the application boots up. Note that the address
# can be modified at any point, but will only take effect after the application is
# restarted. This can be the address of an oracle container running on the same
# machine or a remote machine.
oracle_address = "127.0.0.1:8080"

# Client Timeout is the time that the client is willing to wait for responses from 
# the oracle before timing out.
client_timeout = "500ms"

# MetricsEnabled determines whether oracle metrics are enabled. Specifically
# this enables instrumentation of the oracle client and the interaction between
# the oracle and the app.
metrics_enabled = "false"
```

#### Check logs:

```
journalctl -fu slinky --no-hostname
```

* Successfull Log examples:

{% hint style="info" %}
```
14T19:07:08.296Z","num_prices":65}
May 14 19:07:08 slinky[877177]: {"level":"info","ts":"2024-05-14T19:07:08.547Z","caller":"oracle/oracle.go:163","msg":"oracle updated prices","pid":877177,"process":"oracle","last_sync":"2024-05-14T19:07:08.547Z","num_prices":65}
May 14 19:07:08 slinky[877177]: {"level":"info","ts":"2024-05-14T19:07:08.796Z","caller":"oracle/oracle.go:163","msg":"oracle updated prices","pid":877177,"process":"oracle","last_sync":"2024-05-14T19:07:08.796Z","num_prices":65}
May 14 19:07:09 slinky[877177]: {"level":"info","ts":"2024-05-14T19:07:09.045Z","caller":"oracle/oracle.go:163","msg":"oracle updated prices","pid":877177,"process":"oracle","last_sync":"2024-05-14T19:07:09.045Z","num_prices":65}
May 14 19:07:09 slinky[877177]: {"level":"info","ts":"2024-05-14T19:07:09.296Z","caller":"oracle/oracle.go:163","msg":"oracle updated prices","pid":877177,"process":"oracle","last_sync":"2024-05-14T19:07:09.296Z","num_prices":65}
May 14 19:07:09 slinky[877177]: {"level":"info","ts":"2024-05-14T19:07:09.544Z","caller":"marketmap/fetcher.go:116","msg":"successfully fetched market map data from modu
```
{% endhint %}

