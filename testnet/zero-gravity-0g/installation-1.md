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

# Snapshot

Download snapshot:

Snapshot is updated every 6hours.

Latest Snapshot available is at height:

```markup
sudo systemctl stop 0gd
```

Backup `priv_validator_state.json` & reset data:

```markup
cp $HOME/.0gchain/data/priv_validator_state.json $HOME/.0gchain/priv_validator_state.json.backup
rm -rf $HOME/.0gchain/data
0gchaind tendermint unsafe-reset-all --home $HOME/.0gchain --keep-addr-book
```

Install tool:

```markup
sudo apt-get install wget liblz4-tool aria2 -y
```

Download snapshot:

```markup
aria2c -x5 -s4 http://snapshot.josephtran.xyz/0gchain_25-06-2024-14-32.lz4
```

Extract snapshot:

```markup
lz4 -c -d 0gchain_25-06-2024-14-32.lz4  | tar -x -C $HOME/.0gchain/data
```

Recover  `priv_validator_state.json:`

```markup
mv $HOME/.0gchain/priv_validator_state.json.backup $HOME/.0gchain/data/priv_validator_state.json
```

Restart node:

```markup
sudo systemctl restart 0gd && sudo journalctl -u initiad -f -o cat
```
