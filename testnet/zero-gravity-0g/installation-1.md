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

```
sudo systemctl stop 0gd
cp $HOME/.0gchain/data/priv_validator_state.json $HOME/.0gchain/priv_validator_state.json.backup
rm -rf $HOME/.0gchain/data
0gchaind tendermint unsafe-reset-all --home $HOME/.0gchain --keep-addr-book
sudo apt-get install wget liblz4-tool aria2 -y
aria2c -x5 -s4 http://snapshot.josephtran.xyz/0gchtain_25-06-2024-09-12.lz4
lz4 -c -d 0gchtain_25-06-2024-09-12.lz4  | tar -x -C $HOME/.0gchain/data
mv $HOME/.0gchain/priv_validator_state.json.backup $HOME/.0gchain/data/priv_validator_state.json
sudo systemctl restart 0gd && sudo journalctl -u initiad -f -o cat
```

