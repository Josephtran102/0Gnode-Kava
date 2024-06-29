# Live Peers



```
PEERS=$(curl -s -X POST https://rpc.0gchain.josephtran.xyz -H "Content-Type: application/json" -d '{"jsonrpc":"2.0","method":"net_info","params":[],"id":1}' | jq -r '.result.peers[] | select(.connection_status.SendMonitor.Active == true) | "\(.node_info.id)@\(if .node_info.listen_addr | contains("0.0.0.0") then .remote_ip + ":" + (.node_info.listen_addr | sub("tcp://0.0.0.0:"; "")) else .node_info.listen_addr | sub("tcp://"; "") end)"' | tr '\n' ',' | sed 's/,$//' | awk '{print "\"" $0 "\""}')

sed -i "s/^persistent_peers *=.*/persistent_peers = $PEERS/" "$HOME/.0gchain/config/config.toml"

if [ $? -eq 0 ]; then
    echo -e "Configuration file updated successfully with new peers. by \033[1;33mNodebrand\033[0m"
else
    echo "Failed to update configuration file."
fi
```
