![image](https://github.com/molla202/Source-sourcetest-1/assets/91562185/873a99e6-aff2-4da5-a02a-16f9513b6b6c)

🌟 [Source Protocol Twitter](https://twitter.com/SourceProtocol_)

🌟 [Source Protocol Discord](https://discord.gg/MuPN6kJbCK)

🌟 [Source Protocol Explorer](https://mainnet.itrocket.net/source/staking/sourcevaloper12xtalgwjakzdz4q8s05zkm0a3nkr5wlua77q2k)

🔥 [CoreNode Telegram](https://t.me/corenode)

🔥 [CoreNode Twitter](https://twitter.com/corenodehq)

💬 [Gökhan Molla Twitter](https://twitter.com/gokhan_molla)

💬 [Gökhan Molla Telegram](https://t.me/gokhan_molla)

💬 Sorularınız için yukarıdaki adreslerden ulaşabilirsiniz.


 ## 💻 Sistem Gereksinimleri
| Bileşenler | Minimum Gereksinimler | 
| ------------ | ------------ |
| ✔️ CPU |	8+ |
| ✔️ RAM	| 16+ GB |
| ✔️ Storage	| 500GB+ SSD |


### 🚧 Update ve güncellemeler
```
sudo apt update && sudo apt upgrade -y
sudo apt install curl git wget htop tmux build-essential jq make lz4 gcc unzip -y
```

### 🚧 Go kurulumu
```
cd $HOME
! [ -x "$(command -v go)" ] && {
VER="1.19.3"
wget "https://golang.org/dl/go$VER.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$VER.linux-amd64.tar.gz"
rm "go$VER.linux-amd64.tar.gz"
[ ! -f ~/.bash_profile ] && touch ~/.bash_profile
echo "export PATH=$PATH:/usr/local/go/bin:~/go/bin" >> ~/.bash_profile
source $HOME/.bash_profile
}
[ ! -d ~/go/bin ] && mkdir -p ~/go/bin
```
### 🚧 Varyasyonları ayarlayalım
👉 Not: cüzdan adı ve node adınızı giriniz
```
echo "export WALLET="wallet"" >> $HOME/.bash_profile
echo "export MONIKER="test"" >> $HOME/.bash_profile
echo "export SOURCE_CHAIN_ID="source-1"" >> $HOME/.bash_profile
echo "export SOURCE_PORT="32"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```
### 🚧 Dosyaları çekelim ve kuralım
```
cd $HOME
rm -rf source
git clone https://github.com/Source-Protocol-Cosmos/source.git
cd source
git checkout v3.0.0
make install
```
### 🚧 Ağı başlatalım
👉 Not: node adınızı yazınız
```
sourced config node tcp://localhost:${SOURCE_PORT}657
sourced config keyring-backend os
sourced config chain-id source-1
sourced init "test" --chain-id source-1
```
### 🚧Genesis ve adressbook
```
wget -O $HOME/.source/config/genesis.json https://mainnet-files.itrocket.net/source/genesis.json
wget -O $HOME/.source/config/addrbook.json https://mainnet-files.itrocket.net/source/addrbook.json
```
### 🚧 Seeds ve peer ayarları
```
SEEDS="7347b05f140e4ed5d3da7b26c754a486dc1d2ecd@source-mainnet-seed.itrocket.net:32656"
PEERS="8a812024b8a5b4539878b03ac2f822655831ca5f@source-mainnet-peer.itrocket.net:32656,d3480c6dd884f695e73afc779ca4119224e1d396@89.163.157.252:30656,8b7fd04ce47825b030daf93a20ed63a5422c6471@65.109.94.250:30656,2bf28f66b5240fc8010181aa840515ed46906b93@65.109.24.82:28656,3a3c70cf92899cf7fedc9ceb135c6c64209b42aa@159.69.155.107:26656,bb2263aa7006d52aff93bb40841f0dd486a99463@86.48.2.235:26656,5ac19cb6766f29813bb5c515f7f17d5b2cd972db@173.249.45.4:26656,29aaebc5b17674c3529ac1a4a4d040824aba64fa@54.202.237.247:26656,a43ca8d53c5bc6727dd2eebe7b1a1350bcecbae6@135.181.198.246:26656,0107ac60e43f3b3d395fea706cb54877a3241d21@35.87.85.162:26656,96d63849a529a15f037a28c276ea6e3ac2449695@34.222.1.252:26656"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.source/config/config.toml
```
### 🚧 Portları ayarlayalım
```
sed -i.bak -e "s%:1317%:${SOURCE_PORT}317%g;
s%:8080%:${SOURCE_PORT}080%g;
s%:9090%:${SOURCE_PORT}090%g;
s%:9091%:${SOURCE_PORT}091%g;
s%:8545%:${SOURCE_PORT}545%g;
s%:8546%:${SOURCE_PORT}546%g;
s%:6065%:${SOURCE_PORT}065%g" $HOME/.source/config/app.toml
```
```
sed -i.bak -e "s%:26658%:${SOURCE_PORT}658%g;
s%:26657%:${SOURCE_PORT}657%g;
s%:6060%:${SOURCE_PORT}060%g;
s%:26656%:${SOURCE_PORT}656%g;
s%^external_address = \"\"%external_address = \"$(wget -qO- eth0.me):${SOURCE_PORT}656\"%;
s%:26660%:${SOURCE_PORT}660%g" $HOME/.source/config/config.toml
```
### 🚧 Puring
```
sed -i -e "s/^pruning *=.*/pruning = \"nothing\"/" $HOME/.source/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.source/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"50\"/" $HOME/.source/config/app.toml
```
### 🚧 Gas prometeus ve indexer ayarı
```
sed -i 's/minimum-gas-prices =.*/minimum-gas-prices = "0.25usource"/g' $HOME/.source/config/app.toml
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.source/config/config.toml
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.source/config/config.toml
```
### 🚧 Servis oluşturalım
```
sudo tee /etc/systemd/system/sourced.service > /dev/null <<EOF
[Unit]
Description=Source node
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/.source
ExecStart=$(which sourced) start --home $HOME/.source
Restart=on-failure
RestartSec=5
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```
### 🚧 Snap atalım
```
sourced tendermint unsafe-reset-all --home $HOME/.source
if curl -s --head curl https://mainnet-files.itrocket.net/source/snap_source.tar.lz4 | head -n 1 | grep "200" > /dev/null; then
  curl https://mainnet-files.itrocket.net/source/snap_source.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.source
    else
  echo no have snap
fi
```
### 🚧 Başlatalım
```
sudo systemctl daemon-reload
sudo systemctl enable sourced
sudo systemctl restart sourced
```
### 🚧 Loglara bakalım
```
sudo journalctl -u sourced -f
```
### 🚧 Cüzdan oluşturalım
```
sourced keys add cüzdan-adınız
```
- Cüzdan import
```
sourced keys add cüzdan-adınız --recover
```
### 🚧 Validator olusturalım
Not: moniker ve cüzdan adınızı yazınız
```
sourced tx staking create-validator \
--amount 1000000usource \
--from Cüzdan-adınızı-yazınız \
--commission-rate 0.1 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.01 \
--min-self-delegation 1 \
--pubkey $(sourced tendermint show-validator) \
--moniker "moniker-yazınız" \
--identity "" \
--details "" \
--chain-id source-1 \
--fees 500000usource \
-y
```
### Node silme
```
sudo systemctl stop sourced
sudo systemctl disable sourced
sudo rm -rf /etc/systemd/system/sourced.service
sudo rm $(which sourced)
sudo rm -rf $HOME/.source
sed -i "/SOURCE_/d" $HOME/.bash_profile
```



