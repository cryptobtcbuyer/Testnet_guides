# Haqq Testnet

<div align="center">
  <h1> Haqq Testnet </h1>
</div>

<div align="center">
  <a href="part1">Установка на сервере, где создавали gentx</a> <a href="part2">Установка на другом сервере</a>

</div>



Официальный гайд: [Validators Contest](https://github.com/haqq-network/validators-contest)  
Medium: [Validators Contest](https://github.com/haqq-network/validators-contest)  
Эксплорер: [testnet.manticore.team/haqq](https://testnet.manticore.team/haqq)
 
---

[Установка на сервере, где создавали gentx](#part1)   [Установка на другом сервере](#part2)
---

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
  
  
Создайте служебный файл
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
  
  
Запустите служебный файл и просмотрите логи вашей ноды
```bash
sudo systemctl daemon-reload && \
sudo systemctl enable haqqd && \
sudo systemctl restart haqqd && \
sudo journalctl -u haqqd -f -o cat
```

<br> 
 


<a name="part2"></a> 


 
## Если вы устанавливаете ноду на другом сервере
 
