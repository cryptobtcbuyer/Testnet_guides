# State Sync 

<br>  

>State Sync позволяет не синхронизировать всю базу блокчейна и быстро синхронизировать ноду.  
<br>  

Останавливаем ноду и очищаем базу данных
```bash
sudo systemctl stop hid-noded && hid-noded tendermint unsafe-reset-all --home $HOME/.hid-node
```
Устанавливаем переменные
```bash
SNAP_RPC="http://207.244.253.244:36657"  
# всегда проверяйте, что стейтсинк доступен. Адрес в кавычках должен открываться в браузере.
  
# Вводим одной командой:
LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 100)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)
```
Проверяем
```bash
echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH
# должно быть что-то похожее(цифры будут другие):
# 376080 374080 F0C78FD4AE4DB5E76A298206AE3C602FF30668C521D753BB7C435771AEA47189
```
Добавлем пир
```bash
peers="672c72f28ed5b2a409c1edc2be760e38f76ee4f7@207.244.253.244:36656" \
&& sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.hid-node/config/config.toml
```
Подгружаем addrbook
```bash
wget -O $HOME/.hid-node/config/addrbook.json "https://raw.githubusercontent.com/cryptobtcbuyer/Testnet_guides/main/
Hypersign/addrbook.json"
```
Добавлем значение переменных в config.toml
```bash
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.hid-node/config/config.toml
```

Рестартим ноду и проверяем логи
```bash
sudo systemctl restart hid-noded &&  sudo journalctl -u hid-noded -f -o cat
```

