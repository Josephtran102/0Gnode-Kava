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

# Commands

## 🔑 Key management

### Add new wallet:

```
0gchaind keys add "wallet" --eth
```

### Recover Wallet:

```
0gchaind keys add "wallet" --recover --eth
```

### EVM wallet address:

```
echo "0x$(0gchaind debug addr $(0gchaind keys show "wallet" -a) | grep hex | awk '{print $3}')"
```

### List all Keys:

```
0gchaind keys list
```

### Delete wallet:

```
0gchaind keys delete "wallet"
```

### Export wallet to file:

```
0gchaind keys export "wallet"
```

### Import wallet:

```
0gchaind keys import "wallet" wallet.backup
```

### Query wallet balance:

```
0gchaind q bank balances $(0gchaind keys show "wallet" -a)
```

## 👷Validator Management:

### Create Validator:

```
0gchaind tx staking create-validator \
--amount 100000ua0gi \
--pubkey $(0gchaind tendermint show-validator) \
--moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id zgtendermint_16600-2 \
--commission-rate 0.05 \
--commission-max-rate 0.20 \
--commission-max-change-rate 0.05 \
--min-self-delegation 1 \
--from wallet \
--gas-adjustment 1.5 \
--gas auto \
--gas-prices 0.00252ua0gi \
-y
```

### Edit Validator:

```
0gchaind tx staking edit-validator \
--new-moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--security-contact "YOUR_CONTACT"
--chain-id zgtendermint_16600-2 \
--commission-rate 0.05 \
--from wallet \
--gas-adjustment 1.5 \
--gas auto \
--gas-prices 0.00252ua0gi \
-y
```

### View Validator details:

```
0gchaind q staking validator $(0gchaind keys show wallet --bech val -a)
```

### Unjail:

```
0gchaind tx slashing unjail --from wallet --chain-id zgtendermint_16600-2 --gas-adjustment 1.5 --gas auto --gas-prices 0.00252ua0gi -y
```

## 💰 Token management:

### Send token to another wallet:

```
0gchaind tx bank send "wallet" <TO_WALLET_ADDRESS> 100000ua0gi --from wallet --chain-id zgtendermint_16600-2 --gas-adjustment 1.5 --gas auto --gas-prices 0.00252ua0gi -y
```

### Delegate tokens to yourself:

```
0gchaind tx staking delegate $(0gchaind keys show wallet --bech val -a) 100000ua0gi --from wallet --chain-id zgtendermint_16600-2 --gas-adjustment 1.5 --gas auto --gas-prices 0.00252ua0gi -y
```

### Redelegate tokens to another Validator:

```
0gchaind tx staking redelegate $(0gchaind keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 100000ua0gi --from wallet --chain-id zgtendermint_16600-2 --gas-adjustment 1.5 --gas auto --gas-prices 0.00252ua0gi -y
```

### Withdraw rewards from all Validators:

```
0gchaind tx distribution withdraw-all-rewards --from wallet --chain-id zgtendermint_16600-2 --gas-adjustment 1.5 --gas auto --gas-prices 0.00252ua0gi -y
```

### Withdraw commission & rewards from your validator:

```
0gchaind tx distribution withdraw-rewards $(0gchaind keys show wallet --bech val -a) --commission --from wallet --chain-id zgtendermint_16600-2 --gas-adjustment 1.5 --gas auto --gas-prices 0.00252ua0gi -y
```

### Unbond tokens from your validator:

```
0gchaind tx staking unbond $(0gchaind keys show wallet --bech val -a) 100000ua0gi --from wallet --chain-id zgtendermint_16600-2 --gas-adjustment 1.5 --gas auto --gas-prices 0.00252ua0gi -y
```

## 🗳 Governance:

### Query proposal:

```
0gchaind query gov proposals
```

### Vote Yes/No:

```
0gchaind tx gov vote 1 yes/no --from wallet --chain-id zgtendermint_16600-2 --gas-adjustment 1.5 --gas auto --gas-prices 0.00252ua0gi -y
```

## 🚨 Maintenance

### Get sync status:

```
0gchaind status 2>&1 | jq
```

### Block sync left:

```
local_height=$(0gchaind status | jq -r .sync_info.latest_block_height); network_height=$(curl -s https://0g-rpc.chainad.org/status | jq -r .result.sync_info.latest_block_height); blocks_left=$((network_height - local_height)); echo "Your node height: $local_height"; echo "Network height: $network_height"; echo "Blocks left: $blocks_left"
```

### Get node peers:

```
echo $(0gchaind tendermint show-node-id)'@'$(curl -s ifconfig.me)':'$(cat $HOME/.0gchain/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

### Fetch RPC port:

```
RPC="http://$(wget -qO- eth0.me)$(grep -A 3 "\[rpc\]" $HOME/.0gchain/config/config.toml | egrep -o ":[0-9]+")" && echo $RPC
```

```
curl $RPC/status
```

### Retrieving Node ID and Server IP Address Configuration:

#### Your node:

```
echo $(0gchaind tendermint show-node-id)'@'$(curl -s ipv4.icanhazip.com)':'$(cat $HOME/.0gchain/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

#### From RPC other node:

```
curl -s https://rpc.0gchain.josephtran.xyz/net_info | jq -r '.result.peers[] | select(.node_info.listen_addr | test("^tcp://0.0.0.0") | not) | "\(.node_info.id)@\(.node_info.listen_addr)"'
```

### Change RPC port:

Open for Client:&#x20;

```markup
NEW_RPC_LADDR="tcp://0.0.0.0:26657"
NEW_EVM_ADDRESS="0.0.0.0:8545"
NEW_WS_ADDRESS="0.0.0.0:8546"
NEW_API="eth,txpool,personal,net,debug,web3"
```

```
sed -i \
    -e "s#^\(laddr = \)\"[^\"]*\"#\1\"${NEW_RPC_LADDR}\"#" \
    $HOME/.0gchain/config/config.toml
```

```markup
sed -i \
    -e "s#^\(address = \)\"[^\"]*\"#\1\"${NEW_EVM_ADDRESS}\"#" \
    -e "s#^\(ws-address = \)\"[^\"]*\"#\1\"${NEW_WS_ADDRESS}\"#" \
    -e "s#^\(api = \)\"[^\"]*\"#\1\"${NEW_API}\"#" \
    $HOME/.0gchain/config/app.toml
```

### Remove node:

{% hint style="danger" %}
Please, before proceeding with the next step! All chain data will be lost! Make sure you have backed up your `priv_validator_key.json`!
{% endhint %}

```
cd $HOME
sudo systemctl stop 0gd
sudo systemctl disable 0gd
sudo rm /etc/systemd/system/0gd.service
sudo systemctl daemon-reload
rm -f $(which 0gchaind)
rm -rf $HOME/.0gchain
rm -rf $HOME/0g-chain
```

## ⚙️ Service Management:

### Reload service:

```
sudo systemctl daemon-reload
```

### Enable service:

```
sudo systemctl enable 0gd
```

### Disable service:

```
sudo systemctl disable 0gd
```

### Start service:

```
sudo systemctl start 0gd
```

### Stop service:

```
sudo systemctl stop 0gd
```

### Restart service:

```
sudo systemctl restart 0gd
```

### Check service logs:

```
sudo journalctl -u 0gd -f --no-hostname -o cat
```
