# Tanssi
<a href="https://discord.gg/2ErsZxXyWg" target="_blank">Join team discord <img src="https://user-images.githubusercontent.com/50621007/176236430-53b0f4de-41ff-41f7-92a1-4233890a90c8.png" width="30"/></a>
### Form: https://www.tanssi.network/testnet-campaign/block-producers-waitlist
### Recommended Hardware Requirements 

|   SPEC	    |         FullNode          |       Block Producer      |
| :---------: | :-----------------------: |:-----------------------:  |    
|   **CPU**   |        ≥ 4 Cores          |        ≥ 8 Cores          |
|   **RAM**   |        ≥ 8 GB             |        ≥ 32 GB            |
| **Storage** |        ≥ 500 GB SSD       |        ≥ 1 TB SSD         |
| **NETWORK** |        ≥ 100 Mbps         |        ≥ 500 Mbps         |

## Guide Manual
### Update system and install build tools
```
apt update && apt upgrade -y
apt install curl iptables build-essential git wget jq make gcc nano tmux htop nvme-cli pkg-config libssl-dev libleveldb-dev libgmp3-dev tar clang bsdmainutils ncdu unzip llvm libudev-dev make protobuf-compiler -y
```
### Download the Latest Release
```
wget https://github.com/moondance-labs/tanssi/releases/download/v0.6.3/tanssi-node && \
chmod +x ./tanssi-node
```
### Creat new wallet
```
./tanssi-node key generate -w24
```
### Creat tanssi-data
```
adduser tanssi_service --system --no-create-home
mkdir /var/lib/tanssi-data
sudo chown -R tanssi_service /var/lib/tanssi-data
mv ./tanssi-node /var/lib/tanssi-data
```
### Create the Systemd Service Configuration File
(**Replace** _NODENAME_)
Creat tanssi.service & save file
```
sudo nano /etc/systemd/system/tanssi.service
```
```
[Unit]
Description="Tanssi systemd service"
After=network.target
StartLimitIntervalSec=0

[Service]
Type=simple
Restart=on-failure
RestartSec=10
User=tanssi_service
SyslogIdentifier=tanssi
SyslogFacility=local7
KillSignal=SIGHUP
ExecStart=/var/lib/tanssi-data/tanssi-node \
--chain=dancebox \
--name=NODENAME \
--sync=warp \
--base-path=/var/lib/tanssi-data/para \
--state-pruning=2000 \
--blocks-pruning=2000 \
--collator \
--telemetry-url='wss://telemetry.polkadot.io/submit/ 0' \
--database paritydb \
-- \
--name=NODENAME \
--base-path=/var/lib/tanssi-data/container \
--telemetry-url='wss://telemetry.polkadot.io/submit/ 0' \
-- \
--chain=westend_moonbase_relay_testnet \
--name=NODENAME \
--sync=fast \
--base-path=/var/lib/tanssi-data/relay \
--state-pruning=2000 \
--blocks-pruning=2000 \
--telemetry-url='wss://telemetry.polkadot.io/submit/ 0' \
--database paritydb \

[Install]
WantedBy=multi-user.target
```
```
systemctl enable tanssi.service
systemctl daemon-reload
```
```
systemctl restart tanssi.service && journalctl -f -u tanssi.service
```

### Generate Session Keys
```
curl http://127.0.0.1:9944 -H \
"Content-Type:application/json;charset=utf-8" -d \
  '{
    "jsonrpc":"2.0",
    "id":1,
    "method":"author_rotateKeys",
    "params": []
  }'
```
