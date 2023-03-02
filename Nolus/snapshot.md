## SnapShot 
02.03.23 (1.7 GB) block height 00000


Download the snapshot
```bash
cd $HOME
wget http://0.0.0.0:8000/nolus_snap_.tar.gz
```

Stop the node, make a backup of priv_validator_state and delete the database
```bash
sudo systemctl stop nolusd  && \
cp $HOME/.nolus/data/priv_validator_state.json $HOME/priv_validator_state.json.backup  && \
rm -rf $HOME/.nolus/data/
```

Unpack the snapshot
```bash
tar -C $HOME/.nolus/ -zxvf nolus_snap_.tar.gz --strip-components 3
```

Move the backup back
```
mv $HOME/priv_validator_state.json.backup $HOME/.nolus/data/priv_validator_state.json
```

Restart the node and check the logs
```bash
sudo systemctl restart nolusd && journalctl -fu nolusd -o cat
```

Remove downloaded snapshot to free up space
```bash
cd $HOME && rm -rf nolus_snap_.tar.gz
```

