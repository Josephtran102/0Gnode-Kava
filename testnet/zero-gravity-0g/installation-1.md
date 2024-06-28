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

_<mark style="color:red;">Snapshot is updated every hour.</mark>_

```markup
sudo systemctl stop 0gd
```

Backup `priv_validator_state.json` & reset data:

```markup
cp $HOME/.0gchain/data/priv_validator_state.json $HOME/.0gchain/priv_validator_state.json.backup
```

Reset DB:

```markup
0gchaind tendermint unsafe-reset-all --home $HOME/.0gchain --keep-addr-book
```

Install tool:

```markup
sudo apt-get install wget liblz4-tool aria2 -y
```

Download snapshot:

```markup
aria2c -x5 -s4 https://snapshot.josephtran.xyz/0gchain_snapshot.lz4
```

Extract snapshot:

```markup
lz4 -c -d 0gchain_snapshot.lz4  | tar -x -C $HOME/.0gchain
```

Remove snapshot:

```
cd $HOME
rm -rf 0gchain_snapshot.lz4
```

Move `priv_validator_state.json back:`

```markup
mv $HOME/.0gchain/priv_validator_state.json.backup $HOME/.0gchain/data/priv_validator_state.json
```

Restart node:

```markup
sudo systemctl restart 0gd && sudo journalctl -u 0gd -f -o cat
```

