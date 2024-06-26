# EVM ZK Rollup

## 1. **Clone GitHub Repositories**

```markup
git clone https://github.com/airchains-network/evm-station.git
git clone https://github.com/airchains-network/tracks.git
```

## 2. Setting Up and Running the EVM Station:

```markup
cd evm-station
```

```
go mod tidy
```

```
/bin/bash ./scripts/local-setup.sh
/bin/bash ./scripts/local-start.sh 
```

### **Get Your Private Key of EVM Station**

```
/bin/bash ./scripts/local-keys.sh
```

Result:

```
⚡ root@vmi1797354  ~/evm-station   master ±  /bin/bash ./scripts/local-keys.sh 
45130F31XXXXXXXXXXA3E1C9D5C14359459A87E8B10638E86F502922F3811D24
```

_<mark style="color:yellow;">Save private key to import MetaMask Wallet</mark>_

## 3. Setting Up DA Keys and Running DA Client: (Eigen)

```
wget https://github.com/airchains-network/tracks/releases/download/v0.0.2/eigenlayer
```

```
chmod +x eigenlayer
```

Create and List Keys:

```
./eigenlayer operator keys create --key-type ecdsa [keyname]
```

Result:

```
eigenlayer operator keys create --key-type ecdsa test
? Enter password to encrypt the ecdsa private key:
ECDSA Private Key (Hex):  b3eba201405d5b5f7aaa9adf6bb734dc6c0f448ef64dd39df80ca2d92fca6d7b
Please backup the above private key hex in safe place.

Key location: /home/ubuntu/.eigenlayer/operator_keys/test.ecdsa.key.json
Public Key hex:  f87ee475109c2943038b3c006b8a004ee17bebf3357d10d8f63ef202c5c28723906533dccfda5d76c1da0a9f05cc6d32085ca1af8aaab5a28171474b1ad0aa68
Ethereum Address 0x6a8c0D554a694899041E52a91B4EC3Ff23d8aBD5
```

_<mark style="color:yellow;">Remember this PUB\_KEY</mark>_

## 4. Setting Up and Running Tracks:

Open new tmux tab

```
sudo rm -rf ~/.tracks
```

```
cd tracks
```

```
go mod tidy
```

### **Initiate Sequencer:**

```
go run cmd/main.go init --daRpc "da-rpc" --daKey "daKey" --daType "<da-type>" --moniker "<moniker-name>" --stationRpc "http://127.0.0.1:8545" --stationAPI "http://127.0.0.1:8545" --stationType "evm"
```

* \--daRpc: "**disperser-holesky.eigenda.xyz"**
* \--daKey: \<PUB KEY of DA key>
* \--daType: "eigen"
* \--moniker: "moniker name"

Sample:

```
go run cmd/main.go init --daRpc "disperser-holesky.eigenda.xyz" --daKey "6a822c60ca15c81e824df6c2ff83f050224473bcbaabccab2d8ac084a7749fbb745c3ae96c0b3964083409c39d074cb6000ddb8b093ca3e98bde7c78f794267e" --daType "eigen" --moniker "JosephTran" --stationRpc "http://0.0.0.0:8545" --stationAPI "http://0.0.0.0:8545" --stationType "evm"
```

### **Create Keys for Junction:**

Generate keys for the Junction component using the following command:

```
go run cmd/main.go keys junction --accountName <account-name> --accountPath $HOME/.tracks/junction-accounts/keys
```

Replace `<account-name>`

Sample:

```
go run cmd/main.go keys junction --accountName josephtran --accountPath $HOME/.tracks/junction-accounts/keys
```

Result:

```
[2024-06-27 08:04:19] » Account created: josephtran
[2024-06-27 08:04:19] » Mnemonic: (save your seed phrase)
[2024-06-27 08:04:19] » Address: air124y5xaxaxjj3mfj5ek7kuv2l3347ylumt9jhr9
[2024-06-27 08:04:19] » Please save this mnemonic key for account recovery
[2024-06-27 08:04:19] » Please save this address for future reference
```

Faucet token AMF for this wallet: `air124y5xaxaxjj3mfj5ek7kuv2l3347ylumt9jhr9`

### **Initiate Prover:**

```
go run cmd/main.go prover v1EVM
```

Result:

```
08:05:55 INF compiling circuit08:05:55
INF parsed circuit inputs nbPublic=150 nbSecret=008:05:56 INF building constraint builder nbConstraints=47975
[2024-06-27 08:06:43] » Proving key and Verification key generated and saved successfully
```

### **Create Station on Junction:**

```
go run cmd/main.go create-station --accountName <account-name> --accountPath $HOME/.tracks/junction-accounts/keys --jsonRPC "https://junction-testnet-rpc.synergynodes.com/" --info "EVM Track" --tracks <wallet-address> --bootstrapNode "/ip4/192.168.1.24/tcp/2300/p2p/<node_id>"
```

Sample:

```
go run cmd/main.go create-station --accountName josephtran02 --accountPath $HOME/.tracks/junction-accounts/keys --jsonRPC "https://junction-testnet-rpc.synergynodes.com/" --info "EVM Track" --tracks air15s968ffwy7xykj83fmyhsm47zhptm3kj5l4949 --bootstrapNode "/ip4/53.213.110.159/tcp/2300/p2p/12D3KooWDaaoJcxxxxxxxqZCockmeHyy3QgaKdG5oPwGQNM5X"
```

* \--account-name `josephtran`
* \--jsonRPC `https://junction-testnet-rpc.synergynodes.com/`
* \--track `air124y5xaxaxjj3mfj5ek7kuv2l3347ylumt9jhr9`
* \--bootstrapNode: /ip4/`<IP_VPS>`/tcp/2300/p2p/`<nodeID>`

 You can find Node ID at: `sequencer.toml`

```
nano /root/.tracks/config/sequencer.toml
```

Result:

```
[2024-06-27 08:35:49] » tracks address: air124y5xaxaxjj3mfj5ek7kuv2l3347ylumt9jhr9
accountAddress: air124y5xaxaxjj3mfj5ek7kuv2l3347ylumt9jhr9
[2024-06-27 08:35:49] » Currently user have 1.000000AMF
[2024-06-27 08:36:03] » tx hash: A68BC3CDD523BA300B9474CDA9723B553FC96CF7EB682A77260481A43BAB9DD1
[2024-06-27 08:36:03] » /root/.tracks/config/genesis.json created
[2024-06-27 08:36:03] » vrfPrivKey ID: d929534a15xxxxxxxxxxxxxxxxxxxe67402a5a8ad57542e97b0d
[2024-06-27 08:36:03] » vrfPubKey ID: 86767xxxxxxxxxxxxxxxxxxxxxxx40228c6fb5043a921414
[2024-06-27 08:36:03] » Successfully Created VRF public and private Keys
[2024-06-27 08:36:03] » Successfully created station
```

### **Start Node:**

```
go run cmd/main.go start
```

<figure><img src="../../.gitbook/assets/Screen Shot 2024-06-27 at 21.51.22.png" alt=""><figcaption></figcaption></figure>

### Add Wallet and your network to Meta mask:

Use private key which created by `/bin/bash ./scripts/local-keys.sh`

### Add Network:

Open EVM RPC port to connect from Client:

```
nano /root/.evmosd/config/app.toml
```

```
###############################################################################
###                           JSON RPC Configuration                        ###
###############################################################################

[json-rpc]

# Enable defines if the gRPC server should be enabled.
enable = true

# Address defines the EVM RPC HTTP server address to bind to.
address = "0.0.0.0:8545"
```

Restart EVM Station:

```
/bin/bash ./scripts/local-start.sh 
```

Add wallet from Client:

\*Chain ID will auto get after input URL network:

Choose Network name & Token Symbol

You can see your coin:\


<figure><img src="../../.gitbook/assets/Screen Shot 2024-06-27 at 21.20.43.png" alt=""><figcaption></figcaption></figure>

<figure><img src="../../.gitbook/assets/Screen Shot 2024-06-27 at 21.25.58.png" alt=""><figcaption></figcaption></figure>

## RECOVER EVMstation:

### Eigenlayer operator keys import:

```
./eigenlayer operator keys import --key-type ecdsa josephtran <ECDSA Private Key>
```

```
go run cmd/main.go init --daRpc "disperser-holesky.eigenda.xyz" --daKey "6a822c60ca15c81e824df6c2ff83f050224473bcbaabccab2d8ac084a7749fbb745c3ae96c0b3964083409c39d074cb6000ddb8b093ca3e98bde7c78f794267e" --daType "eigen" --moniker "JosephTran" --stationRpc "http://0.0.0.0:8545" --stationAPI "http://0.0.0.0:8545" --stationType "evm"
```

```
go run cmd/main.go keys import --accountName josephtran --accountPath $HOME/.tracks/junction-accounts/keys --mnemonic "seed phrase"
```

## SERVICE File (optional):

When you get `RPC error` or `Failed to Transact Verify pod`

### 1. Run with bash script

```
nano /root/tracks/run_track.sh
```

Content:

```
#!/bin/bash
# source $HOME/.bash_profile
LOG_FILE=/root/tracks/script.log
GO_FILE=/root/tracks/cmd/main.go
WORK_DIR=/root/tracks
LOCK_FILE=/root/tracks/script.lock

exec 200>$LOCK_FILE
flock -n 200 || exit 1

echo "Script started at $(date)" >> $LOG_FILE

cd $WORK_DIR

if [ -f "$GO_FILE" ]; then
    rollback_success=false

    for i in {1..3}
    do
        go run $GO_FILE rollback >> $LOG_FILE 2>&1
        if [ $? -eq 0 ]; then
            echo "Rollback $i finished successfully at $(date)" >> $LOG_FILE
            rollback_success=true
        else
            echo "Rollback $i failed at $(date)" >> $LOG_FILE
            
            echo "Error details: $(tail -n 1 $LOG_FILE)" >> $LOG_FILE
            rollback_success=false
            break
        fi
        sleep 15
    done

    if $rollback_success; then
        go run $GO_FILE start >> $LOG_FILE 2>&1
        if [ $? -eq 0 ]; then
            echo "Start finished successfully at $(date)" >> $LOG_FILE
        else
            echo "Start failed at $(date)" >> $LOG_FILE
            
            echo "Error details: $(tail -n 1 $LOG_FILE)" >> $LOG_FILE

            for i in {1..3}
            do
                go run $GO_FILE rollback >> $LOG_FILE 2>&1
                if [ $? -eq 0 ]; then
                    echo "Rollback $i finished successfully at $(date)" >> $LOG_FILE
                    rollback_success=true
                    break
                else
                    echo "Rollback $i failed at $(date)" >> $LOG_FILE
                    
                    echo "Error details: $(tail -n 1 $LOG_FILE)" >> $LOG_FILE
                    rollback_success=false
                fi
                sleep 10
            done

            if $rollback_success; then
                echo "Rollback successful after failed start attempt." >> $LOG_FILE
            else
                echo "All attempts to rollback failed. Cannot proceed." >> $LOG_FILE
            fi
        fi
    else
        echo "All attempts to rollback failed. Cannot proceed with start." >> $LOG_FILE
    fi

else
    echo "File $GO_FILE does not exist" >> $LOG_FILE
fi


rm -f $LOCK_FILE

```

_Bash script explain:_

* _run `rollback` 3 times with: `go run cmd/main.go rollback`_
* _run `track`: `go run cmd/main.go start`_

Run Bash script with `tmux`:

```
bash /root/tracks/run_track.sh
```

## Create Service file:

```
nano /etc/systemd/system/tracks.service
```

Service file content:

```
[Unit]
Description=Tracks Service
After=network.target

[Service]
Type=simple
User=root
WorkingDirectory=/root/tracks
ExecStart=/bin/bash /root/tracks/run_track.sh
Restart=always
RestartSec=30
StandardOutput=syslog
StandardError=syslog

[Install]
WantedBy=multi-user.target
```

```
sudo systemctl daemon-reload
sudo systemctl enable tracks
sudo systemctl start tracks
```

Check status and log:

```
sudo systemctl status tracks
```

```
sudo journalctl -u tracks -f
```

