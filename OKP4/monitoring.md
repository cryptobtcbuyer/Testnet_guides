<!--
<img src='https://thumbs.dreamstime.com/z/%D0%B1%D0%B8%D0%B7%D0%BD%D0%B5%D1%81-%D0%B2%D0%B5%D0%B1-%D0%B8%D0%BD%D1%81%D1%82%D1%80%D1%83%D0%BC%D0%B5%D0%BD%D1%82-%D0%B3%D0%BE%D1%80%D0%B8%D0%B7%D0%BE%D0%BD%D1%82%D0%B0%D0%BB%D1%8C%D0%BD%D1%8B%D0%B9-%D0%B1%D0%B0%D0%BD%D0%BD%D0%B5%D1%80-%D1%81%D0%BE%D0%B2%D1%80%D0%B5%D0%BC%D0%B5%D0%BD%D0%BD%D1%8B%D0%B9-%D0%B4%D0%B8%D0%B7%D0%B0%D0%B9%D0%BD-231884478.jpg'>
-->

<div align="center">
  <h1>TenderDuty — monitoring and alerting tool</h1>
</div>
<br> 

Tenderduty is a comprehensive monitoring tool for Tendermint chains. Its primary function is to alert a validator if they are missing blocks, and has many other features.
<br> 
        
- [Installation ↓](#part1)  
- [Setting up alerts in Telegram ↓](#part2)  
- [Setting up alerts in Discord ↓](#part3)  
<br>   
  
<a name="part1"></a> 
 
## Installation

Preparing the server for installation (one command)
```bash
sudo apt update && sudo apt upgrade -y && \
sudo apt install curl build-essential git wget jq make gcc tmux htop nvme-cli pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev -y
```

Installing docker (one command)
```bash
apt update && \
apt install apt-transport-https ca-certificates curl software-properties-common -y && \
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add - && \
add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu focal stable" && \
apt update && \
apt-cache policy docker-ce && \
sudo apt install docker-ce -y && \
docker --version
```

Installing Tenderduty
```bash
mkdir tenderduty && cd tenderduty

docker run --rm ghcr.io/blockpane/tenderduty:latest -example-config >config.yml
```

Download the config optimized for OKP4 and add the necessary values
```bash
wget -O $HOME/tenderduty/config.yml "https://raw.githubusercontent.com/cryptobtcbuyer/config.yml"

nano $HOME/tenderduty/config.yml
```

You need to insert your valoper address
```#valoper_address: okp4valoper...```


For more reliability, you can add more RPC  
<img src='https://github.com/cryptobtcbuyer/Testnet_guides/blob/main/OKP4/assets/config_tenderduty.png'>


Then to exit hit the following keys:
 1. To exit your config.yml file: <kbd>CTRL</kbd> + <kbd>X</kbd>
 2. To select yes to save your modified file: <kbd>Y</kbd>
 3. To confirm the file name and exit: <kbd>ENTER</kbd>
 

Run the container after editing the config
```bash
docker run -d --name tenderduty -p "8888:8888" -p "28686:28686" --restart unless-stopped -v $(pwd)/config.yml:/var/lib/tenderduty/config.yml ghcr.io/blockpane/tenderduty:latest
```

Check logs
```bash
docker logs -f --tail 20 tenderduty
```
<img src='https://github.com/cryptobtcbuyer/Testnet_guides/blob/main/OKP4/assets/run_tenderduty2.png'>

Open monitoring dashboard in the browser
```bash
# find out your address and paste it into the browser
echo -e "\033[0;32mhttp://$(wget -qO- eth0.me):8888/\033[0m"
# http://108.108.108.108:8888/
```
<img src='https://github.com/cryptobtcbuyer/Testnet_guides/blob/main/OKP4/assets/dashboard_tenderduty.png'>

### Great! Everything works!



<a name="part3"></a> 
 
## Setting up alerts in Discord 
<br> 



Restart the container and check logs
```bash
docker restart tenderduty

docker logs -f --tail 20 tenderduty
```
<!--
<img src='https://thumbs.dreamstime.com/discord_alerts.png'>
-->













