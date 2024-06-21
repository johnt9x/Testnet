```
sudo systemctl stop initia

cd $HOME
rm -rf initia
git clone https://github.com/initia-labs/initia.git
cd initia
git checkout v0.2.21
make install

sudo systemctl start initia
sudo journalctl -fu initia -o cat
```
