## SnapShot 
02.03.23 (2.5 GB) block height 1206660


Download the snapshot
```bash
cd $HOME
wget http://194.163.155.84:8000/nolus_snap_1206660.tar.gz
```

Stop the node, make a backup of priv_validator_state and delete the database
```bash
sudo systemctl stop nolusd  && \
cp $HOME/.nolus/data/priv_validator_state.json $HOME/priv_validator_state.json.backup  && \
rm -rf $HOME/.nolus/data/
```

Unpack the snapshot
```bash
tar -C $HOME/.nolus/ -zxvf nolus_snap_1206660.tar.gz --strip-components 3
```

Move the backup back
```
mv $HOME/priv_validator_state.json.backup $HOME/.nolus/data/priv_validator_state.json
```

Download addrbook
```
wget -O $HOME/.nolus/config/addrbook.json "https://raw.githubusercontent.com/cryptobtcbuyer/Testnet_guides/main/Nolus/addrbook.json"
```

Restart the node and check the logs
```bash
sudo systemctl restart nolusd && journalctl -fu nolusd -o cat
```

Remove downloaded snapshot to free up space
```bash
cd $HOME && rm -rf nolus_snap_1206660.tar.gz
```

