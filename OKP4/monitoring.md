<img src='https://github.com/cryptobtcbuyer/Testnet_guides/blob/main/OKP4/assets/monitoring_img.png'>


<div align="center">
  <a href="https://github.com/cryptobtcbuyer/Testnet_guides/tree/main/OKP4">🠔 Back</a>
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
# http://120.120.120.120:8888/
```
<img src='https://github.com/cryptobtcbuyer/Testnet_guides/blob/main/OKP4/assets/dashboard_tenderduty.png'>

### Great! Everything works!


<br> 

<a name="part2"></a> 
 
## Setting up alerts in Telegram 
<br> 
Tenderduty can send notifications about missed blocks to Telegram Group.  <br> 

The first thing we need to do is create our own bot. Launch the[@botfather](https://t.me/BotFather). Enter the ```/newbot``` command and follow the prompts.
You will create a bot and get an api key that you need to keep in a safe place and not show to anyone.
<img src='https://github.com/cryptobtcbuyer/Testnet_guides/blob/main/OKP4/assets/create_bot_tg.png'>  

Create a group and invite the bot you just created to join it.
And to allow the bot to post messages you need to make it an admin.To do this, open the group menu and select the ```Manage group``` section and then ```Administrators```.  
<img src='https://github.com/cryptobtcbuyer/Testnet_guides/blob/main/OKP4/assets/admin_bot_tg.png'>  

You need to link the bot and the channel to your Tenderduty configuration. To do so, you need to retrieve the channel ID from your newly created group. 

Launch the bot [@username_to_id_bot](https://t.me/username_to_id_bot) and send him an invitation to your group. 
To get a link to the group, go back to the menu ``Manage group``  and select the section ``Invite links``  
<img src='https://github.com/cryptobtcbuyer/Testnet_guides/blob/main/OKP4/assets/invite_tg.png'>  

After you send the link, the bot will send you the ID of your group  
<img src='https://github.com/cryptobtcbuyer/Testnet_guides/blob/main/OKP4/assets/id_group_tg.png'>  

Add the group id and api key that you received from the @botfather to the config
<img src='https://github.com/cryptobtcbuyer/Testnet_guides/blob/main/OKP4/assets/config_tg.png'>  

Restart the container and check logs
```bash
docker restart tenderduty

docker logs -f --tail 20 tenderduty
```
You will receive alerts if the node starts skipping blocks or the RPC server crashes  
<img src='https://github.com/cryptobtcbuyer/Testnet_guides/blob/main/OKP4/assets/tg_tenderduty.png'>  



<br> 

<a name="part3"></a> 
 
## Setting up alerts in Discord 
<br> 
Tenderduty can send notifications about missed blocks to Discord.  
 
To do this, you need to create a new server and new channel.  
<img src='https://github.com/cryptobtcbuyer/Testnet_guides/blob/main/OKP4/assets/create_discord_tenderduty.png'>

Open the server menu and select Integration section and go to the webhooks.  
<img src='https://github.com/cryptobtcbuyer/Testnet_guides/blob/main/OKP4/assets/webhooks_discord_tenderduty.png'>

Create a webhook, copy the URL and paste it into the config.  
<img src='https://github.com/cryptobtcbuyer/Testnet_guides/blob/main/OKP4/assets/copy_webhooks_discord_tenderduty.png'>
<img src='https://github.com/cryptobtcbuyer/Testnet_guides/blob/main/OKP4/assets/paste_webhooks_discord_tenderduty.png.png'>

Restart the container and check logs
```bash
docker restart tenderduty

docker logs -f --tail 20 tenderduty
```
You will receive alerts if the node starts skipping blocks or the RPC server crashes  
<img src='https://github.com/cryptobtcbuyer/Testnet_guides/blob/main/OKP4/assets/discord_tenderduty.png'>












