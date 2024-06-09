# I. Analog Node Installation:
Docs: (https://docs.analog.one/documentation/node-operators/running-a-timechain-node)[https://docs.analog.one/documentation/node-operators/running-a-timechain-node]
## 1. First, ensure you’ve installed Docker:
```
docker compose vesion
```
- Install:
```
sudo apt update
sudo apt install apt-transport-https ca-certificates curl software-properties-common
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt update
sudo apt install docker-ce
docker --version
```
## 2. Next, download the latest Timechain image:
```
docker pull analoglabs/timechain:latest
```
- Check & Open port: 9944, 30303:
```
sudo ss -tuln | grep 9944
sudo ss -tuln | grep 30303
```
```
sudo ufw allow 9944/tcp
sudo ufw allow 30303/tcp
```

- Now run the following command:
```
docker run -d -p 9944:9944 -p 30403:30303 --name JosephTran analoglabs/timechain --base-path /data --rpc-external --rpc-methods=Unsafe --unsafe-rpc-external --name JosephTran --telemetry-url 'wss://telemetry.analog.one/submit 0'
```
Result:
```
ubuntu@josephtran  ~  docker run -d -p 9944:9944 -p 30403:30303 --name JosephTran analoglabs/timechain --base-path /data --rpc-external --rpc-methods=Unsafe --unsafe-rpc-external --name JosephTran --telemetry-url 'wss://telemetry.analog.one/submit 0'

`a62be6b4f8147e59109f8c4bcbe7503d272a27846dd9a54290e533a702349bc3`
```
- Check Container ID:
```
docker compose ps
```
Result:

```
ubuntu@josephtran  ~  docker ps
CONTAINER ID   IMAGE                                  COMMAND                  CREATED             STATUS             PORTS                                                                                                                                                           NAMES
a62be6b4f814   analoglabs/timechain                   "/timechain-node --b…"   About an hour ago   Up About an hour   0.0.0.0:9944->9944/tcp, :::9944->9944/tcp, 0.0.0.0:30403->30303/tcp, :::30403->30303/tcp                                                                        JosephTran
3b1b6e1fbe5f   farcasterxyz/hubble:latest             "npx pm2-runtime sta…"   23 hours ago        Up 23 hours        0.0.0.0:2281-2283->2281-2283/tcp, :::2281-2283->2281-2283/tcp                                                                                                   hubble-hubble-1
7f1d60ca8e05   grafana/grafana:10.0.3                 "/run.sh"                3 days ago          Up 23 hours        0.0.0.0:3000->3000/tcp, :::3000->3000/tcp                                                                                                                       hubble-grafana-1
409f029134b3   graphiteapp/graphite-statsd:1.1.10-5   "/entrypoint"            3 days ago          Up 23 hours        80/tcp, 2003-2004/tcp, 2013-2014/tcp, 2023-2024/tcp, 8080/tcp, 8125/tcp, 0.0.0.0:8125->8125/udp, :::8125->8125/udp, 0.0.0.0:8126->8126/tcp, :::8126->8126/tcp   hubble-statsd-1
```
Or check for Time-node only:
```
docker ps | grep timechain-node
```

- Check logs:
```
ubuntu@josephtran  ~  docker logs a62be6b4f8147e59109f8c4bcbe7503d272a27846dd9a54290e533a702349bc3
```
Or check logs with Container NAMES:
```
docker logs JosephTran
```
Result:
```
2024-06-08 02:58:05 Timechain Node    
2024-06-08 02:58:05 ✌️  version 0.5.0-unknown    
2024-06-08 02:58:05 ❤️  by Analog Devs <https://github.com/analog-labs>, 2017-2024    
2024-06-08 02:58:05 📋 Chain specification: Analog Testnet    
2024-06-08 02:58:05 🏷  Node name: JosephTran    
2024-06-08 02:58:05 👤 Role: FULL    
2024-06-08 02:58:05 💾 Database: ParityDb at /data/chains/anlogcc1/paritydb/full    
2024-06-08 02:58:06 🔨 Initializing Genesis block/state (state: 0x81b4…0774, header-hash: 0x0614…b943)    
2024-06-08 02:58:06 👴 Loading GRANDPA authority set from genesis on what appears to be first startup.    
2024-06-08 02:58:06 👶 Creating empty BABE epoch changes on what appears to be first startup.    
2024-06-08 02:58:06 Using default protocol ID "sup" because none is configured in the chain specs    
2024-06-08 02:58:06 🏷  Local node identity is: 12D3KooWPAzvKjq2rZfy47E4MGms5c4Ug6VvMq6u2Jqf3Ht988wx    
2024-06-08 02:58:06 💻 Operating system: linux    
2024-06-08 02:58:06 💻 CPU architecture: x86_64    
2024-06-08 02:58:06 💻 Target environment: gnu    
2024-06-08 02:58:06 💻 CPU: Intel(R) Xeon(R) E-2386G CPU @ 3.50GHz    
2024-06-08 02:58:06 💻 CPU cores: 6    
2024-06-08 02:58:06 💻 Memory: 31984MB    
2024-06-08 02:58:06 💻 Kernel: 5.15.0-107-lowlatency    
2024-06-08 02:58:06 💻 Linux distribution: Ubuntu 22.04.4 LTS    
2024-06-08 02:58:06 💻 Virtual machine: no    
2024-06-08 02:58:06 📦 Highest known block at #0    
2024-06-08 02:58:06 〽️ Prometheus exporter started at 127.0.0.1:9615    
2024-06-08 02:58:06 Running JSON-RPC server: addr=0.0.0.0:9944, allowed origins=["http://localhost:*", "http://127.0.0.1:*", "https://localhost:*", "https://127.0.0.1:*", "https://polkadot.js.org"]    
2024-06-08 02:58:11 💤 Idle (0 peers), best: #0 (0x0614…b943), finalized #0 (0x0614…b943), ⬇ 1.3kiB/s ⬆ 0.6kiB/s   
```
Check log sync status:
```
docker logs -f JosephTran
```
# II. Generate and Register your Session Keys:
```
echo '{"id":1,"jsonrpc":"2.0","method":"author_rotateKeys","params":[]}' | websocat -n1 -B 99999999 ws://127.0.0.1:9944
```
Result:
```
✘ ubuntu@josephtran  ~  echo '{"id":1,"jsonrpc":"2.0","method":"author_rotateKeys","params":[]}' | websocat -n1 -B 99999999 ws://127.0.0.1:9944

{"jsonrpc":"2.0","result":"0x90a3477db1c362ebaaf744f93ba3964866c3062037b86d79480hthth7i776u7ubc0d7783b9a98f6ca2fd64c1573eac5eebeb93ec1e233f7b8888db2c4fc1fbddc099c11f4b9fc5f4f1a1c6b80fc32ad0abb8a9db3ba198949f1f34262018cf2b","id":1}
```
Use this value for Session Keys:
`"0x90a3477db1c362ebaaf744f93ba3964866c3062037b86d79480hthth7i776u7ubc0d7783b9a98f6ca2fd64c1573eac5eebeb93ec1e233f7b8888db2c4fc1fbddc099c11f4b9fc5f4f1a1c6b80fc32ad0abb8a9db3ba198949f1f34262018cf2b"`


If you get error can not generate Session keys:
- Install Rust:
```
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
rustc --version
```
Install websocat:
```
sudo apt update
sudo apt install -y libssl-dev pkg-config
```
```
cargo install websocat
```
And run Generate again.

### Customize (Optional - Testing)
```
docker run -d -p 9944:9944 -p 30403:30303 --name Analog-Joseph analoglabs/timechain --validator --base-path /data --rpc-external --rpc-methods=Unsafe --unsafe-rpc-external --rpc-max-connections=200 --name JosephTran --telemetry-url 'wss://telemetry.analog.one/submit 1' --in-peers=50
```
Testing:
```
docker run -d -p 9944:9944 -p 30403:30303 --name Analog -v /home/ubuntu/Analog/data:/data -v /home/ubuntu/Analog/node-key:/data/node-key analoglabs/timechain --validator --base-path /data --rpc-external --rpc-methods=Unsafe --unsafe-rpc-external --rpc-max-connections=200 --name JosephTran --telemetry-url 'wss://telemetry.analog.one/submit 1' --in-peers=50 --node-key-file /data/node-key/secret_seed.txt 
```

