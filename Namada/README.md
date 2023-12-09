<img src='https://github.com/cryptobtcbuyer/Testnet_guides/blob/main/Namada/assets/pr_cover.png'>


<div align="center">
     <h1>How to join as a pre-genesis validator in the Namada testnet</h1>
</div>
<br> 

The official installation guide can be found at the [docs.namada.net](https://docs.namada.net/)

Website — [namada.net](https://namada.net)  
Twitter — [@namada](https://twitter.com/namada)  
Github — [namada](https://github.com/anoma/namada)  
Discord — [namada](https://discord.com/invite/namada)
 

<br> 

## Table of contents
- [Hardware Requirements ↓](#hardware)  
- [Installation ↓](#installation)  
- [Create validator keys ↓](#keys)
- [Create Pull Request ↓](#github)  

<br>   
  
<a name="hardware"></a> 
 
## Hardware Requirements
Minimum Hardware Requirements
- CPU: 4 
- RAM: 8GB 
- SSD: 1TB
- Open ports: 26656, 26657
  
<br>

<a name="installation"></a> 

## Installation
Update packages and Install dependencies (one command)
```bash
sudo apt update && sudo apt upgrade -y && \
sudo apt install curl build-essential git wget jq make gcc tmux htop nvme-cli pkg-config libssl-dev tar clang bsdmainutils ncdu protobuf-compiler unzip libudev-dev libleveldb-dev -y
```

Install GO v1.20.5 (one command)
```bash
cd $HOME && \
ver="1.20.5" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
go version
```

Install Rust
```bash
sudo curl https://sh.rustup.rs -sSf | sh -s -- -y
source "$HOME/.cargo/env"
cargo --version
```

Install PROTOC 
```bash
cd $HOME && rustup update  
PROTOC_ZIP=protoc-23.3-linux-x86_64.zip
curl -OL https://github.com/protocolbuffers/protobuf/releases/download/v23.3/$PROTOC_ZIP 
sudo unzip -o $PROTOC_ZIP -d /usr/local bin/protoc 
sudo unzip -o $PROTOC_ZIP -d /usr/local 'include/*' 
rm -f $PROTOC_ZIP 
protoc --version
```

Install Namada
```bash
NAMADA_TAG="v0.28.0"
git clone https://github.com/anoma/namada && cd namada && git fetch && git checkout $NAMADA_TAG 
make build-release
```


Install CometBFT
```bash
COMETBFT_TAG="v0.37.2"
cd $HOME && git clone https://github.com/cometbft/cometbft.git && cd cometbft && git checkout $COMETBFT_TAG
make build
```

Move binary
```bash
cd $HOME && cp $HOME/cometbft/build/cometbft /usr/local/bin/cometbft && \
cp "$HOME/namada/target/release/namada" /usr/local/bin/namada && \
cp "$HOME/namada/target/release/namadac" /usr/local/bin/namadac && \
cp "$HOME/namada/target/release/namadan" /usr/local/bin/namadan && \
cp "$HOME/namada/target/release/namadaw" /usr/local/bin/namadaw && \
cp "$HOME/namada/target/release/namadar" /usr/local/bin/namadar
```

Check binary version
```bash
namada --version
#v0.28.0
cometbft version
#v0.37.2
```

<br>  

<a name="keys"></a> 

## Create your validator keys


Generating new keys.
Be sure to save the password and the mnemonic phrase
```bash
ALIAS="NAME_OF_YOUR_VALIDATOR"
namadaw --pre-genesis key gen --alias $ALIAS
```

Generate an established account.
```bash
TX_FILE_PATH="$HOME/.local/share/namada/pre-genesis/transactions.toml"
namadac utils init-genesis-established-account --path $TX_FILE_PATH --aliases $ALIAS

# The command will ouptut the generated address of the established account. It is useful to save this address for later use. <br>
# Derived established account address: tnam123....
```

Generate a pre-genesis validator account.<br>
Enter the necessary data into the variables and run the command
```bash
ESTABLISHED_ACCOUNT_ADDRESS="THE_ADDRESS_FROM_THE_PREVIOUS_STEP"
EMAIL="YOUR_EMAIL"
IP="IP_ADDRESS_OF_YOUR_SERVER:26656"

namadac utils init-genesis-validator --address $ESTABLISHED_ACCOUNT_ADDRESS --alias $ALIAS --net-address $IP --commission-rate 0.05 --max-commission-rate-change 0.01 --self-bond-amount 1000000 --email $EMAIL --path $TX_FILE_PATH

# Successful execution will output the new validator address:
# Validator account address: tnam321...
```

Signing transactions
```bash
namadac utils sign-genesis-txs --path $TX_FILE_PATH --output $HOME/.local/share/namada/pre-genesis/signed-transactions.toml --alias $ALIAS

# Your public signed transactions TOML has been written to /root/.local/share/namada/pre-genesis/signed-transactions.toml
```

You can print the signed-transactions.toml by running
```bash
cat $HOME/.local/share/namada/pre-genesis/signed-transactions.toml
```

<br>  

<a name="github"></a> 

## Create Pull Request

Go to the directory
[https://github.com/anoma/namada-testnets/tree/main/namada-public-testnet-15](https://github.com/anoma/namada-testnets/tree/main/namada-public-testnet-15) 

Add a new file
<img src='https://github.com/cryptobtcbuyer/Testnet_guides/blob/main/Namada/assets/new_file.png'>

If you have been a previous validator, name your PR "Update $ALIAS.toml" (substitute $ALIAS for your validator name). <br>
Otherwise name it "Create $ALIAS.toml". <br>
Paste the contents of the signed-transactions.toml file, and add your contact details according to the sample
```
- email
- discord
- elements/matrix handle (optional)
- twitter (optional)
```

and hit "Commit changes" and after "Propose changes"
<img src='https://github.com/cryptobtcbuyer/Testnet_guides/blob/main/Namada/assets/commit2.png'>


Now create pull request
<img src='https://github.com/cryptobtcbuyer/Testnet_guides/blob/main/Namada/assets/pr2.png'>

<br> 

✅ **Everything is ready!** Stay tuned for further announcements
<img src='https://github.com/cryptobtcbuyer/Testnet_guides/blob/main/Namada/assets/open2.png'>



> **If your PR is not merged, you can run the post-genesis validator**
> 
> Remember, you must not need to be a genesis-validator in order to test the network effectively.

