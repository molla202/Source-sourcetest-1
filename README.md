![image](https://github.com/molla202/Source-sourcetest-1/assets/91562185/873a99e6-aff2-4da5-a02a-16f9513b6b6c)

🌟 [Source Protocol Twitter](https://twitter.com/SourceProtocol_)

🌟 [Source Protocol Discord](https://discord.gg/MuPN6kJbCK)

🌟 [Source Protocol Explorer](https://testnet.itrocket.net/source/staking)

🔥 [CoreNode Telegram](https://t.me/corenode)

🔥 [CoreNode Twitter](https://twitter.com/corenodehq)

💬 [Gökhan Molla Twitter](https://twitter.com/gokhan_molla)

💬 [Gökhan Molla Telegram](https://t.me/gokhan_molla)

💬 Sorularınız için yukarıdaki adreslerden ulaşabilirsiniz.


 ## 💻 Sistem Gereksinimleri
| Bileşenler | Minimum Gereksinimler | 
| ------------ | ------------ |
| ✔️ CPU |	4+ |
| ✔️ RAM	| 8+ GB |
| ✔️ Storage	| 300GB+ SSD |


### Update ve güncellemeler
```
sudo apt update && sudo apt upgrade -y
sudo apt install curl git wget htop tmux build-essential jq make lz4 gcc unzip -y
```

### Go kurulumu
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
### Varyasyonları ayarlayalım
Not: cüzdan adı ve node adınızı giriniz
```
echo "export WALLET="cüzdan-adı"" >> $HOME/.bash_profile
echo "export MONIKER="node-adı"" >> $HOME/.bash_profile
echo "export SOURCE_CHAIN_ID="sourcetest-1"" >> $HOME/.bash_profile
echo "export SOURCE_PORT="24"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```
### Dosyaları çekelim ve kuralım
```
cd $HOME
rm -rf source
git clone https://github.com/Source-Protocol-Cosmos/source.git
cd source
git checkout v3.0.0
make install
```
### Ağı başlatalım
Not: node adınızı yazınız
```
sourced config node tcp://localhost:${SOURCE_PORT}657
sourced config keyring-backend os
sourced config chain-id sourcetest-1
sourced init "node-adı" --chain-id sourcetest-1
```
### Genesis ve adressbook
```
wget -O $HOME/.source/config/genesis.json https://testnet-files.itrocket.net/source/genesis.json
wget -O $HOME/.source/config/addrbook.json https://testnet-files.itrocket.net/source/addrbook.json
```
### Seeds ve peer ayarları
```
SEEDS="eca738b67fd23381f9a72717bea757c1d291ed2b@source-testnet-seed.itrocket.net:24656"
PEERS="a47f3b354e75478c0dfe22ad2b937ad07c9bcf3c@source-testnet-peer.itrocket.net:24656,06885ec73c228de552c4120ab50c78152b20fc9d@162.19.236.64:26656,6cbd311e49982122e3b28549302d5666c53ed0cf@65.109.104.118:61056,e127f3f7277b76887423458d8f775e33f58ff80a@65.109.65.248:28656,854048fcfb453297742b76cc5c6b7555eeb25110@213.239.207.175:31656,636c7206a1a9a817768766a1f243d27398159028@144.76.97.251:36656,8145d4d13511e7f89dbd257f51ed5d076941f12f@164.92.98.12:26656,330b14f94d3bbe6c4059f31bd8fbf9960cf1387e@185.144.99.3:26656,a98484ac9cb8235bd6a65cdf7648107e3d14dab4@116.202.231.58:12856,dc6b2c1fcb38aa42c750adc73875660655dacb4a@173.249.45.4:26656,7659c361ca4b0bbbe118d0b91c390e1ad51f7c2c@65.21.248.172:26656"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.source/config/config.toml
```
### Portları ayarlayalım
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
### Puring
```
sed -i -e "s/^pruning *=.*/pruning = \"nothing\"/" $HOME/.source/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.source/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"50\"/" $HOME/.source/config/app.toml
```
### Gas prometeus ve indexer ayarı
```
sed -i 's/minimum-gas-prices =.*/minimum-gas-prices = "1usource"/g' $HOME/.source/config/app.toml
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.source/config/config.toml
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.source/config/config.toml
```
### Servis oluşturalım
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
### Snap atalım
```
sourced tendermint unsafe-reset-all --home $HOME/.source
if curl -s --head curl https://testnet-files.itrocket.net/source/snap_source.tar.lz4 | head -n 1 | grep "200" > /dev/null; then
  curl https://testnet-files.itrocket.net/source/snap_source.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.source
    else
  echo no have snap
fi
```
### Başlatalım
```
sudo systemctl daemon-reload
sudo systemctl enable sourced
sudo systemctl restart sourced
```
### Loglara bakalım
```
sudo journalctl -u sourced -f
```
### Cüzdan oluşturalım
```
sourced keys add cüzdan-adınız
```
- Cüzdan import
```
sourced keys add cüzdan-adınız --recover
```
### Validator olusturalım
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
--chain-id sourcetest-1 \
--fees 200000usource \
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



