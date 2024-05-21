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
ver="1.21.3" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
go version
```

## 3. Git clone:

```markup
git clone -b v0.1.0 https://github.com/0glabs/0g-chain.git
./0g-chain/networks/testnet/install.sh
source .profile
```

## 4. Variable settings:

```markup
echo 'export MONIKER="your_node_name"' >> ~/.bash_profile
echo 'export CHAIN_ID="zgtendermint_16600-1"' >> ~/.bash_profile
echo 'export WALLET_NAME="wallet_name"' >> ~/.bash_profile
echo 'export RPC_PORT="26657"' >> ~/.bash_profile
source $HOME/.bash_profile
```
