<img src='https://github.com/cryptobtcbuyer/Testnet_guides/blob/main/Composable/assets/composable_cover.png'>


<div align="center">
    <h1>BANKSY-TESTNET-2  Installation Guide</h1>
</div>
<br> 

Website — [composable.finance](https://composable.finance)  
Twitter — [@ComposableFin](https://twitter.com/ComposableFin)  
Github — [Composable-networks](https://github.com/notional-labs/composable-networks)  
Medium — [Composable Finance](https://composablefi.medium.com)   
Discord — [Composable Finance](https://discord.com/invite/composable)
 
 

<br> 
        
- [Installation ↓](#installation)  
- [State Sync ↓](#statesync)  
- [Snapshot ↓](#snapshot)  

<br>   
  
<a name="installation"></a> 
 
## Installation

Preparing the server for installation (one command)
```bash
sudo apt update && sudo apt upgrade -y && \
sudo apt install curl build-essential git wget jq make gcc tmux htop nvme-cli pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev -y
```


Installing GO v1.20 (one command)
```bash
cd $HOME && \
ver="1.20" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
go version
```
<br>  

Set variables
```bash
CHAIN="banksy-testnet-2" && \
MONIKER="<YOUR__MONIKER>" && \
WALLET="<YOUR_WALLET_NAME>"

echo "export MONIKER=$MONIKER" >> $HOME/.bash_profile && \
echo "export WALLET=$WALLET" >> $HOME/.bash_profile && \
echo "export CHAIN=$CHAIN" >> $HOME/.bash_profile && \
source $HOME/.bash_profile
```

Build binary 
```bash
git clone https://github.com/notional-labs/composable-testnet
cd composable-testnet 
git checkout v2.3.1
make install
```

Init your node
```bash
banksyd init $MONIKER --chain-id $CHAIN && \
banksyd config chain-id $CHAIN
```

Add wallet
```bash
banksyd keys add $WALLET 
```

Download Genesis
```bash
wget -O $HOME/.banksy/config/genesis.json "https://raw.githubusercontent.com/notional-labs/composable-networks/main/testnet-2/genesis.json"
```
Download addrbook
```bash
wget -O $HOME/.banksy/config/addrbook.json "https://raw.githubusercontent.com/cryptobtcbuyer/Testnet_guides/main/Composable/addrbook.json"
```

Configure Peers
```bash
SEEDS="3f472746f46493309650e5a033076689996c8881@composable-testnet.rpc.kjnodes.com:15959"
sed -i.bak -e "s/^seeds *=.*/seeds = \"$SEEDS\"/" $HOME/.banksy/config/config.toml


PEERS=7a4247261bad16289428543538d8e7b0c785b42c@135.181.22.94:26656,1d1b341ee37434cbcf23231d89fa410aeb970341@65.108.206.74:36656,73190b1ec85654eeb7ccdc42538a2bb4a98b2802@194.163.165.176:46656,837d9bf9a4ce4d8fd0e7b0cbe51870a2fa29526a@65.109.85.170:58656,085e6b4cf1f1d6f7e2c0b9d06d476d070cbd7929@banksy.sergo.dev:11813,d9b5a5910c1cf6b52f79aae4cf97dd83086dfc25@65.108.229.93:27656
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.banksy/config/config.toml
```

Create a service file
```bash
sudo tee /etc/systemd/system/banksyd.service > /dev/null <<EOF
[Unit]
Description=banksyd
After=network-online.target

[Service]
User=$USER
ExecStart=$(which banksyd) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

Launch node and check logs
```bash
sudo systemctl daemon-reload && \
sudo systemctl enable banksyd && \
sudo systemctl restart banksyd && \
sudo journalctl -u banksyd -f -o cat
```

Wait until the node is fully synchronized.
```bash
banksyd status | jq
# The node is synchronized if the value in the "catching_up" line is false
```
Create a validator
```bash
banksyd tx staking create-validator \
--amount=1000000upica \
--pubkey=$(banksyd tendermint show-validator) \
--moniker=$MONIKER \
--chain-id=$CHAIN \
--commission-rate="0.05" \
--commission-max-rate="0.20" \
--commission-max-change-rate="0.1" \
--min-self-delegation="1" \
--fees=5000upica \
--from=$WALLET \
--identity="" \
--website="" \
--details="" \
-y
```

<br>  

<a name="statesync"></a> 
 
## State Sync

(optional)
<br>  

>State Sync allows you not to synchronize the entire blockchain database and quickly synchronize the node.
<br>  

Stop the node and clear the database
```bash
sudo systemctl stop banksyd && banksyd tendermint unsafe-reset-all --home $HOME/.banksy
```
Setting variables
```bash
SNAP_RPC="http://194.163.165.176:46657"  
# always check that the statesink is available. The address in quotes should open in the browser.
  
# Enter with one command:
LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 100)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)
```
Check
```bash
echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH
# there must be something similar (the numbers will be different):
# 376080 374080 F0C78FD4AE4DB5E76A298206AE3C602FF30668C521D753BB7C435771AEA47189
```
Adding a peer
```bash
peers="73190b1ec85654eeb7ccdc42538a2bb4a98b2802@194.163.165.176:46656" \
&& sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.banksy/config/config.toml
```
Adding a addrbook
```bash
wget -O $HOME/.banksy/config/addrbook.json "https://raw.githubusercontent.com/cryptobtcbuyer/Testnet_guides/main/Composable/addrbook.json"
```
Adding the variables to config.toml
```bash
sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.banksy/config/config.toml
```

Restart the node and check the logs
```bash
sudo systemctl restart banksyd  &&  sudo journalctl -u banksyd  -f -o cat
```

<br> 


