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

# Commands

## 🔑 Key management

### Add new wallet:

```
initiad keys add "wallet" 
```

### Recover Wallet:

```
initiad keys add "wallet" --recover 
```

### EVM wallet address:

```
echo "0x$(initiad debug addr $(0gchaind keys show "wallet" -a) | grep hex | awk '{print $3}')"
```

### List all Keys:

```
initiad keys list
```

### Delete wallet:

```
initiad keys delete "wallet"
```

### Export wallet to file:

```
initiad keys export "wallet"
```

### Import wallet:

```
initiad keys import "wallet" wallet.backup
```

### Query wallet balance:

```
initiad q bank balances $(initiad keys show "wallet" -a)
```

## 👷Validator Management:

### Create Validator:

```
initiad tx mstaking create-validator \
--amount 1000000uinit \
--pubkey $(initiad tendermint show-validator) \
--moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id initiation-1 \
--commission-rate 0.05 \
--commission-max-rate 0.20 \
--commission-max-change-rate 0.05 \
--from wallet \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 0.15uinit \
-y
```

### Edit Validator:

```
initiad tx mstaking edit-validator \
--new-moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id initiation-1 \
--commission-rate 0.05 \
--from wallet \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 0.15uinit \
-y
```

### View Validator details:

```
initiad q staking validator $(initiad keys show wallet --bech val -a)
```

### Unjail:

```
initiad tx slashing unjail --from wallet --chain-id initiation-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.15uinit -y
```

## 💰 Token management:

### Send token to another wallet:

```
initiad tx bank send wallet <TO_WALLET_ADDRESS> 1000000uinit --from wallet --chain-id initiation-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.15uinit -y
```

### Delegate tokens to yourself:

```
initiad tx mstaking delegate $(initiad keys show wallet --bech val -a) 1000000uinit --from wallet --chain-id initiation-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.15uinit -y
```

### Redelegate tokens to another Validator:

```
initiad tx mstaking redelegate $(initiad keys show wallet --bech val -a) <TO_VALOPER_ADDRESS> 1000000uinit --from wallet --chain-id initiation-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.15uinit -y
```

### Withdraw rewards from all Validators:

```
initiad tx distribution withdraw-all-rewards --from wallet --chain-id initiation-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.15uinit -y
```

### Withdraw commission & rewards from your validator:

```
initiad tx distribution withdraw-rewards $(initiad keys show wallet --bech val -a) --commission --from wallet --chain-id initiation-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.15uinit -y
```

### Unbond tokens from your validator:

```
initiad tx mstaking unbond $(initiad keys show wallet --bech val -a) 1000000uinit --from wallet --chain-id initiation-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.15uinit -y
```

## 🗳 Governance:

### Query proposal:

```
initiad query gov proposals
```

### Vote Yes/No:

```
initiad tx gov vote 1 yes/no --from wallet --chain-id initiation-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.15uinit -y
```

## 🚨 Maintenance

### Get sync status:

```
initiad status 2>&1 | jq
```

### Check sync status:

```
local_height=$(initiad status | jq -r .sync_info.latest_block_height); network_height=$(curl -s https://rpc-initia-testnet.trusted-point.com/status | jq -r .result.sync_info.latest_block_height); blocks_left=$((network_height - local_height)); echo "Your node height: $local_height"; echo "Network height: $network_height"; echo "Blocks left: $blocks_left"
```

### Get node peers:

```
echo $(initiad tendermint show-node-id)'@'$(curl -s ifconfig.me)':'$(cat $HOME/.initia/config/config.toml | sed -n '/Address to listen for incoming connection/{n;p;}' | sed 's/.*://; s/".*//')
```

### Remove node:

{% hint style="danger" %}
Please, before proceeding with the next step! All chain data will be lost! Make sure you have backed up your `priv_validator_key.json`!
{% endhint %}

```
cd $HOME
sudo systemctl stop initia.service
sudo systemctl disable initia.service
sudo rm /etc/systemd/system/initia.service
sudo systemctl daemon-reload
rm -f $(which initiad)
rm -rf $HOME/.initia
rm -rf $HOME/initia
```

## ⚙️ Service Management:

### Reload service:

```
sudo systemctl daemon-reload
```

### Enable service:

```
sudo systemctl enable initiad
```

### Disable service:

```
sudo systemctl disable initiad
```

### Start service:

```
sudo systemctl start initiad
```

### Stop service:

```
sudo systemctl stop initiad
```

### Restart service:

```
sudo systemctl restart initiad
```

### Check service logs:

```
sudo journalctl -u initiad -f --no-hostname -o cat
```

### Fetch RPC port:

```
RPC="http://$(wget -qO- eth0.me)$(grep -A 3 "\[rpc\]" $HOME/.initia/config/config.toml | egrep -o ":[0-9]+")" && echo $RPC
```

```
curl $RPC/status
```
