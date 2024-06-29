# Snapshot

```
sudo systemctl stop initiad.service
cp $HOME/.initia/data/priv_validator_state.json $HOME/.initia/priv_validator_state.json.backup
initiad tendermint unsafe-reset-all --home $HOME/.initia --keep-addr-book
sudo apt-get install wget liblz4-tool aria2 -y
aria2c -x5 -s4 https://files3.blacknodes.net/initiatestnet/initiation-1_1836782.tar.lz4
lz4 -c -d initiation-1_1836782.tar.lz4  | tar -x -C $HOME/.initia/data
mv $HOME/.initia/priv_validator_state.json.backup $HOME/.initia/data/priv_validator_state.json
sudo systemctl restart initiad && sudo journalctl -u initiad -f -o cat
```
