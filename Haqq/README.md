<div align="center">
  <h1> Haqq Testnet </h1>
</div>



Сайт — [islamiccoin.net](https://islamiccoin.net/)  
Medium — [Islamic Coin](https://medium.com/islamic-coin)  
Twitter — [@Islamic_Coin](https://twitter.com/islamic_coin)  
Github — [haqq-network](https://github.com/haqq-network)  
Официальный гайд — [Validators Contest](https://github.com/haqq-network/validators-contest)  
Эксплорер — [testnet.manticore.team/haqq](https://testnet.manticore.team/haqq)  
 
 #
 <br> 

- [Установка на сервере, где создавали gentx ↓](#part1)  
- [Установка на другом сервере ↓](#part2)  
- [Создание валидатора ↓](#part3)  
- [State Sync ↓](#part4)  
- [Задания ↓](#part5)  
- [Полезные команды ↓](#part6)  

<br> 


<a name="part1"></a> 
 
## Если вы устанавливаете ноду на том же сервере, где создавали gentx
 
<br> 

> ⚠️   
> Перед началом установки, сделайте резервную копию каталога `.haqqd/config/.`  
> Там находится `priv_validator_key.json`, без которого вы не сможете продолжить участие в тестнете, в случае его потери. 
<br> 
 
Обновите бинарный файл haqqd до версии 1.0.3.
```bash
cd $HOME/haqq && \
git fetch && \
git checkout v1.0.3 && \
make install
```
  
Убедитесь, что версия и commit правильные:
```bash
haqqd version --long | head
# version: '"1.0.3"'
# commit: 58215364d5be4c9ab2b17b2a80cf89f10f6de38a
```
  
Удалите старый genesis и загрузите genesis.json на свой сервер в папку .haqqd
```bash
rm -rf $HOME/.haqqd/config/genesis.json && cd $HOME/.haqqd/config/ && wget https://raw.githubusercontent.com/haqq-network/validators-contest/master/genesis.json
```
  
Проверьте genesis.json
```bash
sha256sum $HOME/.haqqd/config/genesis.json
# 8c79dda3c8f0b2b9c0f5e770136fd6044ea1a062c9272d17665cb31464a371f7
```

Добавьте сиды и пиры
```bash
seeds="62bf004201a90ce00df6f69390378c3d90f6dd7e@seed2.testedge2.haqq.network:26656,23a1176c9911eac442d6d1bf15f92eeabb3981d5@seed1.testedge2.haqq.network:26656"  
peers="b3ce1618585a9012c42e9a78bf4a5c1b4bad1123@65.21.170.3:33656,952b9d918037bc8f6d52756c111d0a30a456b3fe@213.239.217.52:29656,85301989752fe0ca934854aecc6379c1ccddf937@65.109.49.111:26556,d648d598c34e0e58ec759aa399fe4534021e8401@109.205.180.81:29956,f2c77f2169b753f93078de2b6b86bfa1ec4a6282@141.95.124.150:20116,eaa6d38517bbc32bdc487e894b6be9477fb9298f@78.107.234.44:45656,37513faac5f48bd043a1be122096c1ea1c973854@65.108.52.192:36656,d2764c55607aa9e8d4cee6e763d3d14e73b83168@66.94.119.47:26656,fc4311f0109d5aed5fcb8656fb6eab29c15d1cf6@65.109.53.53:26656,297bf784ea674e05d36af48e3a951de966f9aa40@65.109.34.133:36656,bc8c24e9d231faf55d4c6c8992a8b187cdd5c214@65.109.17.86:32656"  
sed -i -e 's|^seeds *=.*|seeds = "'$seeds'"|; s|^persistent_peers *=.*|persistent_peers = "'$peers'"|' $HOME/.haqqd/config/config.toml
```


Загрузите addrbook
```bash
rm $HOME/.haqqd/config/addrbook.json  && wget -O $HOME/.haqqd/config/addrbook.json "https://raw.githubusercontent.com/cryptobtcbuyer/Testnet_guides/main/Haqq/addrbook.json"

```
  
  
Создайте сервисный файл
```bash
sudo tee /etc/systemd/system/haqqd.service > /dev/null <<EOF
[Unit]
Description=Haqq Node
After=network.target

[Service]
User=$USER
Type=simple
ExecStart=$(which haqqd) start
Restart=on-failure
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
  
  
Запустите сервисный файл и просмотрите логи вашей ноды
```bash
sudo systemctl daemon-reload && \
sudo systemctl enable haqqd && \
sudo systemctl restart haqqd && \
sudo journalctl -u haqqd -f -o cat
```


<br> 
 

<a name="part2"></a> 
 
## Если вы устанавливаете ноду на другом сервере
<br> 

> ⚠️  
> Если вы хотите перейти на другой сервер — убедитесь, что вы сохранили `mnemonic` и `priv_validator_key.json` с сервера, где вы делали gentx.  
> Без мнемоники вы не сможете востановить свой кошелек, а без `priv_validator_key.json` запустить валидатора.  



<br> 

Обновите дистрибутив  и установите необходимые пакеты
```bash
sudo apt update && sudo apt upgrade -y && \
sudo apt install curl tar wget clang pkg-config libssl-dev jq build-essential bsdmainutils git make ncdu gcc git jq chrony liblz4-tool -y
```

Установите Go 1.18.3
```bash
cd $HOME && \
ver="1.18.3" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
go version
```

 
Установите бинарный файл 
```bash
cd $HOME && git clone https://github.com/haqq-network/haqq && \
cd haqq && \
git checkout v1.0.3 && \
make install 
```
  
Убедитесь, что версия и commit правильные:
```bash
haqqd version --long | head
# version: '"1.0.3"'
# commit: 58215364d5be4c9ab2b17b2a80cf89f10f6de38a
```

Инициализируйте ноду
> Данные в <> меняйте на свои значение и убирайте сами <>

```bash
haqqd init <YOURMONIKER> --chain-id haqq_54211-2 && \
haqqd config chain-id haqq_54211-2
```

Восстановите свой кошелек
```bash
haqqd keys add <YOURWALLET> --recover
```

📥 Загрузите сохраненный `priv_validator_key.json` на сервер. Путь должен выглядеть так `/.haqqd/config/priv_validator_key.json`


Удалите старый genesis и загрузите genesis.json в папку .haqqd
```bash
rm -rf $HOME/.haqqd/config/genesis.json && cd $HOME/.haqqd/config/ && wget https://raw.githubusercontent.com/haqq-network/validators-contest/master/genesis.json
```
  
Проверьте genesis.json
```bash
sha256sum $HOME/.haqqd/config/genesis.json
# 8c79dda3c8f0b2b9c0f5e770136fd6044ea1a062c9272d17665cb31464a371f7
```

Добавьте сиды и пиры
```bash
seeds="62bf004201a90ce00df6f69390378c3d90f6dd7e@seed2.testedge2.haqq.network:26656,23a1176c9911eac442d6d1bf15f92eeabb3981d5@seed1.testedge2.haqq.network:26656"  
peers="b3ce1618585a9012c42e9a78bf4a5c1b4bad1123@65.21.170.3:33656,952b9d918037bc8f6d52756c111d0a30a456b3fe@213.239.217.52:29656,85301989752fe0ca934854aecc6379c1ccddf937@65.109.49.111:26556,d648d598c34e0e58ec759aa399fe4534021e8401@109.205.180.81:29956,f2c77f2169b753f93078de2b6b86bfa1ec4a6282@141.95.124.150:20116,eaa6d38517bbc32bdc487e894b6be9477fb9298f@78.107.234.44:45656,37513faac5f48bd043a1be122096c1ea1c973854@65.108.52.192:36656,d2764c55607aa9e8d4cee6e763d3d14e73b83168@66.94.119.47:26656,fc4311f0109d5aed5fcb8656fb6eab29c15d1cf6@65.109.53.53:26656,297bf784ea674e05d36af48e3a951de966f9aa40@65.109.34.133:36656,bc8c24e9d231faf55d4c6c8992a8b187cdd5c214@65.109.17.86:32656"  
sed -i -e 's|^seeds *=.*|seeds = "'$seeds'"|; s|^persistent_peers *=.*|persistent_peers = "'$peers'"|' $HOME/.haqqd/config/config.toml
```


Загрузите addrbook
```bash
rm $HOME/.haqqd/config/addrbook.json  && wget -O $HOME/.haqqd/config/addrbook.json "https://raw.githubusercontent.com/cryptobtcbuyer/Testnet_guides/main/Haqq/addrbook.json"
```
  
  
Создайте сервисный файл
```bash
sudo tee /etc/systemd/system/haqqd.service > /dev/null <<EOF
[Unit]
Description=Haqq Node
After=network.target

[Service]
User=$USER
Type=simple
ExecStart=$(which haqqd) start
Restart=on-failure
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
  
  
Запустите сервисный файл и просмотрите логи вашей ноды
```bash
sudo systemctl daemon-reload && \
sudo systemctl enable haqqd && \
sudo systemctl restart haqqd && \
sudo journalctl -u haqqd -f -o cat
```
 
 
<br> 
<a name="part3"></a> 
 
## Создание валидатора
<br>  

  
Дождитесь полной синхронизации ноды.
```bash
curl -s localhost:26657/status
# Нода синхронизирована, если в строчке "catching_up" значение false
```

Чтобы создать валидатора нам нужно получить токены из крана.

Для этого экспортируем *private_key* нашего кошелька
```bash
haqqd keys export <YOURWALLET> --unarmored-hex --unsafe
```
Импортируем кошелек в Metamask:
1. Открываем Metamask  
1. `Меню` ——> `Импортировать счет`  
1.  Выбираем тип «Закрытый ключ» и вставляем в поле *private_key* из предыдущей команды.
1.  Нажимаем «Импорт»

Переходим на сайт крана https://testedge2.haqq.network/  
Подключаем кошелек, добавляем предложенную сеть, логинимся черех гитхаб и запрашиваем 1 токен.

Проверяем, что мы все сделали правильно и токен пришел на кошелек
```bash
haqqd q bank balances <YOURWALLETADDRESS>
# amount: "1000000000000000000"
```

Создаем валидатора
```bash
haqqd tx staking create-validator \
--chain-id haqq_54211-2 \
--amount 1000000000000000000aISLM \
--pubkey $(haqqd tendermint show-validator) \
--commission-rate 0.05 \
--commission-max-rate 0.2 \
--commission-max-change-rate 0.1 \
--min-self-delegation "1000000" \
--moniker "<YOURMONIKER>" \
--from <YOURWALLET> \
--gas=auto \
--fees 500aISLM
```

> ⚠️   
> Обязательно делаем резервную копию `priv_validator_key.json` 
<br> 
 
 
Находим своего валидатора в эксплорере  
https://testnet.manticore.team/haqq/staking

>Если валидатор не создался, проверьте в эксплорере txhash транзакции — там будет описана ошибка 




  
 
<br> 
<a name="part4"></a> 
 
## State Sync
<br>  

Останавливаем ноду и очищаем базу данных
```bash
sudo systemctl stop haqqd && haqqd tendermint unsafe-reset-all --home $HOME/.haqqd
```
Устанавливаем переменные
```bash
SNAP_RPC="http://142.132.202.50:11601"  
# всегда провейте изначально, что стейтсинк доступен. Адрес в кавычках должен открываться в браузере.
  
# Вводим одной командой:
LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)
```
Проверяем
```bash
echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH
# должно быть что-то похожее(цифры будут другие) на:
# 376080 374080 F0C78FD4AE4DB5E76A298206AE3C602FF30668C521D753BB7C435771AEA47189
```
Добавлем пир
```bash
peers="23ff658b56fbb8bc73372973a34733ff5d79b435@142.132.202.50:11604" \
&& sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.haqqd/config/config.toml
```
Подгружаем addrbook
```bash
rm $HOME/.haqqd/config/addrbook.json && wget -O $HOME/.haqqd/config/addrbook.json "https://raw.githubusercontent.com/cryptobtcbuyer/Testnet_guides/main/Haqq/addrbook.json"
```
Добавлем значние переменных в config.toml
```bash
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.haqqd/config/config.toml
```

Рестартим ноду и проверяем логи
```bash
sudo systemctl restart haqqd &&  sudo journalctl -u haqqd -f -o cat
```


<br> 

<a name="part5"></a> 
 
## Задания 

*Скоро*

<br> 


<br> 

<a name="part6"></a> 
 
## Полезные команды
<br> 
