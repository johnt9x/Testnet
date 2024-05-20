# Airchains
<a href="https://discord.gg/airchains" target="_blank">Join team discord <img src="https://user-images.githubusercontent.com/50621007/176236430-53b0f4de-41ff-41f7-92a1-4233890a90c8.png" width="30"/></a>
### Recommended Hardware Requirements

|   SPEC      |       Recommend          |
| :---------: | :-----------------------:|
|   **CPU**   |        4 Cores           |
|   **RAM**   |        16 GB             |
| **Storage** |        1TB SS            |
| **NETWORK** |        1 Gbps            |
|   **OS**    |        Ubuntu 22.04      |
|   **Port**  |        117               | 


**RPC:** https://rpc-initia.johnvnb.com/

**API:** https://api-initia.johnvnb.com/

**GRPC:** https://grpc-initia.johnvnb.com/

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
cd $HOME
rm -rf initia
git clone https://github.com/initia-labs/initia.git
cd initia
git checkout v0.2.12
make install
```
## Setup systemd
```
sudo tee /etc/systemd/system/initia.service > /dev/null << EOF
[Unit]
Description=initia node
After=network-online.target
[Service]
User=$USER
ExecStart=$(which initiad) start
Restart=on-failure
RestartSec=10
LimitNOFILE=10000
[Install]
WantedBy=multi-user.target
EOF
sudo systemctl daemon-reload
sudo systemctl enable initia
```
## Initialize Node
```
initiad init "$MONIKER" --chain-id initation-1

```
## Download Genesis & Addrbook
```
wget -O $HOME/.initia/config/addrbook.json https://rpc-initia-testnet.trusted-point.com/addrbook.json
curl -Ls https://initia.s3.ap-southeast-1.amazonaws.com/initiation-1/genesis.json > \
         $HOME/.initia/config/genesis.json
```
## Configure
```
SEEDS=""
PEERS="028c3db8c2658887f5714432c21cb53d8c8aa0be@155.133.27.151:14656,9ea146b73504a8cb2d8269f50b736c1d3e4f54a4@154.12.229.0:53456,b9b043fb2f836c0dafe9faa287a5f49c4b05cd13@46.38.241.12:53456,cea76d6adcadd2ed767beaa1646698fae5b6b21d@213.199.50.157:53456,8daae173125d3ba7d7e75c84ff4224e3ee0beb84@45.94.58.167:26656,576e44d27629e338f0a3dcf5e6b20f0f73fa1ade@185.205.244.202:26756,27c51d5794e563eab00dd2aed7119694a4ffba23@172.105.117.175:26656,7d569a33ac52fa77f396445196fc65c2360e629d@89.58.56.169:26656,0280ec4c7070bc50a20198eb01a9980abab72185@38.242.146.122:26656"
sed -i 's|^seeds *=.*|seeds = "'$SEEDS'"|; s|^persistent_peers *=.*|persistent_peers = "'$PEERS'"|' $HOME/.initia/config/config.toml
sed -i 's|^pruning *=.*|pruning = "custom"|g' $HOME/.initia/config/app.toml
sed -i 's|^pruning-keep-recent  *=.*|pruning-keep-recent = "100"|g' $HOME/.initia/config/app.toml
sed -i 's|^pruning-interval *=.*|pruning-interval = "10"|g' $HOME/.initia/config/app.toml
sed -i 's|^snapshot-interval *=.*|snapshot-interval = 0|g' $HOME/.initia/config/app.toml

sed -i -e 's/external_address = \"\"/external_address = \"'$(curl httpbin.org/ip | jq -r .origin)':11756\"/g' ~/.initia/config/config.toml
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0.15uinit,0.01uusdc\"|" $HOME/.initia/config/app.toml
sed -i 's|^prometheus *=.*|prometheus = true|' $HOME/.initia/config/config.toml
```
## Custom Port
```
sed -i -e "s%^proxy_app = \"tcp://127.0.0.1:26658\"%proxy_app = \"tcp://127.0.0.1:11758\"%; s%^laddr = \"tcp://127.0.0.1:26657\"%laddr = \"tcp://127.0.0.1:11757\"%; s%^pprof_laddr = \"localhost:6060\"%pprof_laddr = \"localhost:11760\"%; s%^laddr = \"tcp://0.0.0.0:26656\"%laddr = \"tcp://0.0.0.0:11756\"%; s%^prometheus_listen_addr = \":26660\"%prometheus_listen_addr = \":11766\"%" $HOME/.initia/config/config.toml
sed -i -e "s%^address = \"tcp://localhost:1317\"%address = \"tcp://0.0.0.0:11717\"%; s%^address = \":8080\"%address = \":11780\"%; s%^address = \"localhost:9090\"%address = \"0.0.0.0:11790\"%; s%^address = \"0.0.0.0:9091\"%address = \"0.0.0.0:11791\"%; s%:8545%:11745%; s%:8546%:11746%; s%:6065%:11765%" $HOME/.initia/config/app.toml
```

## Start Node
```
sudo systemctl restart initia && journalctl -fu initia -o cat
```
