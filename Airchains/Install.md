# Airchains
<a href="https://discord.gg/airchains" target="_blank">Join team discord <img src="https://user-images.githubusercontent.com/50621007/176236430-53b0f4de-41ff-41f7-92a1-4233890a90c8.png" width="30"/></a>
### Recommended Hardware Requirements

|   SPEC      |       Recommend          |
| :---------: | :-----------------------:|
|   **CPU**   |        4 Cores           |
|   **RAM**   |        8 GB              |
| **Storage** |        500 GB            |
| **NETWORK** |        1 Gbps            |
|   **OS**    |        Ubuntu 22.04      |
|   **Port**  |        103               | 


**RPC:** https://rpc-airchain.johnvnb.com/

**API:** https://api-airchain.johnvnb.com/

**GRPC:** https://grpc-airchain.johnvnb.com/

**Explorer:** https://explorer.johnvnb.com/Airchains

**Genesis,json:** https://snapshot-airchains.johnvnb.com/genesis.json

**Addrbook.json:** https://snapshot-airchains.johnvnb.com/addrbook.json

**Snapshot:** https://github.com/johnt9x/Testnet/Airchains/blob/main/Snapshot

**Statesync:** https://github.com/johnt9x/Testnet/Airchains/blob/main/Statesync

# Manual node setup
Here you have to put name of your moniker (validator) that will be visible in explorer
```
MONIKER="NODENAME"
```
## Update and install packages for compiling
```
sudo apt -q update
sudo apt -qy install curl git jq lz4 build-essential
sudo apt -qy upgrade
```
### Install go
```
sudo rm -rf /usr/local/go
curl -Ls https://go.dev/dl/go1.22.2.linux-amd64.tar.gz | sudo tar -xzf - -C /usr/local
eval $(echo 'export PATH=$PATH:/usr/local/go/bin' | sudo tee /etc/profile.d/golang.sh)
eval $(echo 'export PATH=$PATH:$HOME/go/bin' | tee -a $HOME/.profile)
```
## Build binary
```
cd $HOME && mkdir -p go/bin/
wget https://github.com/airchains-network/junction/releases/download/v0.1.0/junctiond
chmod +x junctiond
mv junctiond $HOME/go/bin/
```
## Cosmovisor Setup
```
sudo tee /etc/systemd/system/junction.service > /dev/null <<EOF
[Unit]
Description=junction
After=network-online.target

[Service]
User=$USER
ExecStart=$(which junctiond) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
sudo systemctl daemon-reload
sudo systemctl enable junction
```
## Initialize Node
```
junctiond init $MONIKER --chain-id junction
```
## Download Genesis & Addrbook
```
wget -O $HOME/.junction/config/genesis.json "https://snapshot-airchains.johnvnb.com/genesis.json"
wget -O $HOME/.junction/config/addrbook.json "https://snapshot-airchains.johnvnb.com/addrbook.json"
```
## Configure
```
sed -i 's/minimum-gas-prices =.*$/minimum-gas-prices = "0.001amf"/' $HOME/.junction/config/app.toml
external_address=$(wget -qO- eth0.me) 
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:26656\"/" $HOME/.junction/config/config.toml
peers="594e9bfa5c92c3491975bd2346fcbb9eb8dfc2a8@86.48.21.214:10356,48887cbb310bb854d7f9da8d5687cbfca02b9968@35.200.245.190:26656,2d1ea4833843cc1433e3c44e69e297f357d2d8bd@5.78.118.106:26656,de2e7251667dee5de5eed98e54a58749fadd23d8@34.22.237.85:26656,1918bd71bc764c71456d10483f754884223959a5@35.240.206.208:26656,ddd9aade8e12d72cc874263c8b854e579903d21c@178.18.240.65:26656,eb62523dfa0f9bd66a9b0c281382702c185ce1ee@38.242.145.138:26656,0305205b9c2c76557381ed71ac23244558a51099@162.55.65.162:26656,086d19f4d7542666c8b0cac703f78d4a8d4ec528@135.148.232.105:26656,3e5f3247d41d2c3ceeef0987f836e9b29068a3e9@168.119.31.198:56256,8b72b2f2e027f8a736e36b2350f6897a5e9bfeaa@131.153.232.69:26656,6a2f6a5cd2050f72704d6a9c8917a5bf0ed63b53@93.115.25.41:26656,e09fa8cc6b06b99d07560b6c33443023e6a3b9c6@65.21.131.187:26656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.junction/config/config.toml
seeds=""
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.junction/config/config.toml
sed -i 's/max_num_inbound_peers =.*/max_num_inbound_peers = 50/g' $HOME/.junction/config/config.toml
sed -i 's/max_num_outbound_peers =.*/max_num_outbound_peers = 50/g' $HOME/.junction/config/config.toml
pruning="custom"
pruning_keep_recent="1000"
pruning_keep_every="0"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.junction/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.junction/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.junction/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.junction/config/app.toml
indexer="null" &&
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.junction/config/config.toml
```
## Custom Port
```
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:10358\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:10357\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:10360\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:10356\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":10366\"%" $HOME/.junction/config/config.toml
sed -i -e "s%^address = \"tcp://localhost:1317\"%address = \"tcp://0.0.0.0:10317\"%; s%^address = \":8080\"%address = \":10380\"%; s%^address = \"localhost:9090\"%address = \"0.0.0.0:10390\"%; s%^address = \"localhost:9091\"%address = \"0.0.0.0:10391\"%; s%:8545%:10345%; s%:8546%:10346%; s%:6065%:10365%" $HOME/.junction/config/app.toml
sed -i \
  -e 's|^chain-id *=.*|chain-id = "junction"|' \
  -e 's|^keyring-backend *=.*|keyring-backend = "test"|' \
  -e 's|^node *=.*|node = "tcp://localhost:10357"|' \
  $HOME/.junction/config/client.toml
```

## Start Node
```
sudo systemctl restart junction && journalctl -fu junction -o cat
```
