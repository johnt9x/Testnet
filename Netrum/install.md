# Netrum
<a href="https://discord.gg/2ErsZxXyWg" target="_blank">Join team discord <img src="https://user-images.githubusercontent.com/50621007/176236430-53b0f4de-41ff-41f7-92a1-4233890a90c8.png" width="30"/></a>
### Recommended Hardware Requirements 

|   SPEC	    |         FullNode          |       Block Producer      |
| :---------: | :-----------------------: |:-----------------------:  |    
|   **CPU**   |        ≥ 4 Cores          |        ≥ 8 Cores          |
|   **RAM**   |        ≥ 8 GB             |        ≥ 32 GB            |
| **Storage** |        ≥ 500 GB SSD       |        ≥ 1 TB SSD         |
| **NETWORK** |        ≥ 100 Mbps         |        ≥ 500 Mbps         |

## Guide Manual

## 1. Install Dependencies

Run the following commands on Ubuntu/Debian:
```bash
sudo apt update && sudo apt install -y curl bc jq speedtest-cli nodejs npm
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt install -y nodejs
```
## 2. Download & Install Netrum Lite Node

```bash
git clone https://github.com/NetrumLabs/netrum-lite-node.git
cd netrum-lite-node
npm install
npm install -g npm@11.5.2
npm link
netrum
```
## 3. Wallet & Domain Setup

1. **Create a new wallet**

   ```bash
   netrum-new-wallet
   ```
   👉 Save both **wallet address & private key**. Import this wallet into MetaMask.

2. **Buy a Base domain**

   * Go to [https://www.base.org/names](https://www.base.org/names)
   * Connect your new MetaMask wallet
   * Buy a domain with **10+ characters** (cheaper: \~0.4 USD)

3. **Check your basename**

   ```bash
   netrum-check-basename
   ```
   
## 4. Node Registration

1. Check Node ID:

   ```bash
   netrum-node-id
   ```
2. Sign Node:

   ```bash
   netrum-node-sign
   ```
3. Register Node (requires \~1.5 USD ETH fee):

   ```bash
   netrum-node-register
   ```
   
## 5. Sync & Mining

* Sync node with network:

  ```bash
  netrum-sync
  ```

  View logs:

  ```bash
  netrum-sync-log
  ```

  *(press `CTRL+C` to exit logs)*

* Start mining:

  ```bash
  netrum-mining
  ```

  Check mining logs:

  ```bash
  netrum-mining-log
  ```

  *(press `CTRL+C` to exit logs)*

---

## 6. Claim Rewards

* Claim when you have at least **10 NPT tokens**:

  ```bash
  netrum-claim
  ```
* After claiming, go to the **official Discord** and verify to get your role.

---

## 7. Other Useful Commands

* Import wallet:

  ```bash
  netrum-import-wallet
  ```
* Show wallet info:

  ```bash
  netrum-wallet
  ```
* Export private key:

  ```bash
  netrum-wallet-key
  ```
* Remove wallet files:

  ```bash
  netrum-wallet-remove
  ```
* Update CLI:

  ```bash
  netrum-update
  ```

## 8. Stop & Remove Node (Cleanup)

If you want to **stop and delete the node completely**:

```bash
sudo systemctl stop netrum-node
sudo systemctl disable netrum-node
sudo rm /etc/systemd/system/netrum-node.service
sudo systemctl daemon-reload
rm -rf ~/netrum-lite-node
npm unlink -g netrum-lite-node
```

✅ You’re done! Your node is now set up, registered, mining, and ready to claim rewards.

👉 Do you want me to also make a **short copy-paste version (only commands)** for quick setup on VPS?

