</p>

<p align="center">
  <img height="200" height="auto" src="https://user-images.githubusercontent.com/78480857/203667557-6645f459-7082-440c-8b81-7302b6233010.png">

</p>

</p>

<p align="center">
    Perseverance-Chainflip
  
</p>

# Chainflip

  ### Minimum specification
- OS      : Ubuntu 20.04 (PLEASE ONLY USE THIS VERSION)
- CPU     : 4 Cores 
- RAM     : 8 GB
- Disk    : 50 GB (may increase overtime)
- Bandwith: 1GBps connection (recommended), 100 GB bandwidth combined up/down per month

  ### Prerequisites
- [Discord](https://discord.gg/VQUjZ6sm)
- [Website](https://stake-perseverance.chainflip.io/)
- [Twitter](https://twitter.com/chainflip)
- [Telegram](https://t.me/chainflip_io_chat)
- [Official docs](https://docs.chainflip.io/perseverance-validator-documentation/)

# 1. Open Port 30333 & 8078
```
sudo ufw allow ssh & sudo ufw allow 30333 && sudo ufw allow 8078 && sudo ufw enable
```

# 2. Add User
### a. create ```flip``` user with this command:
```
sudo useradd -s /bin/bash -d /home/flip/ -m -G sudo flip 
```

### b. add password for ```flip``` user
```
sudo passwd flip
```

### c. create directory & give permission
```
mkdir /home/flip/.ssh
sudo chown -R flip:flip /home/flip/.ssh/
sudo chmod 0700 /home/flip/.ssh/
```

### d. move to ```flip``` user
```
su - flip
```

# 3. Download & Install Chainflip 
### a. download Chainflip GPG keys
```
sudo mkdir -p /etc/apt/keyrings
curl -fsSL repo.chainflip.io/keys/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/chainflip.gpg
```
verify the keys with command 
```
gpg --show-keys /etc/apt/keyrings/chainflip.gpg
```
*if it run properly, output will be:

![chengflip2](https://user-images.githubusercontent.com/78480857/203667569-b8923112-b06f-412e-8994-308e20de525d.png)
    
### b. add chainflip
```
echo "deb [signed-by=/etc/apt/keyrings/chainflip.gpg] https://repo.chainflip.io/perseverance/ focal main" | sudo tee /etc/apt/sources.list.d/chainflip.list
```

### c. install Chainflip
```
sudo apt-get update
sudo apt-get install -y chainflip-cli chainflip-node chainflip-engine
```

# 4. Wallet Configuration & Faucet
(prefer use new wallet)

### a. create key directory
```
sudo mkdir /etc/chainflip/keys
```

### b. create key's file
```
echo -n "YOUR_PRIVATE_KEY" |  sudo tee /etc/chainflip/keys/ethereum_key_file
```
replace ```YOUR_PRIVATE_KEY``` with your Metamask wallet private key

### c. request faucet

Goerli:
- https://faucet.paradigm.xyz/
- https://goerlifaucet.com/

FLIP Token:
- Join Chainflip Discord, go to #join-testnet channel and click the button, then go to #faucet channel and use command ```!drip <your_address>```

### d. create node key
```
chainflip-node key generate
```
**Save all output in the safe place for backup** 

### e. save signing key
**create**
```
SECRET_SEED=<YOUR_SECRET_SEED>
```
* replace <YOUR_SECRET_SEED> with your secret seed from step before

**save**
```
echo -n "${SECRET_SEED:2}" | sudo tee /etc/chainflip/keys/signing_key_file
```

### f. generate node key
```
sudo chainflip-node key generate-node-key --file /etc/chainflip/keys/node_key_file
```

### g. give permission
```
sudo chmod 600 /etc/chainflip/keys/ethereum_key_file
sudo chmod 600 /etc/chainflip/keys/signing_key_file
sudo chmod 600 /etc/chainflip/keys/node_key_file
history -c
```

**BACKUP** keys file (location: ```/etc/chainflip/keys/)


# 5. Create edit Configuration
### a. create directory
```
sudo mkdir -p /etc/chainflip/config
```

### b. create file
```
sudo nano /etc/chainflip/config/Default.toml
```

### c. create config
```
# Default configurations for the CFE
[node_p2p]
node_key_file = "/etc/chainflip/keys/node_key_file"
ip_address="YOUR_IP_ADDRESS"
port = "8078"

[state_chain]
ws_endpoint = "ws://127.0.0.1:9944"
signing_key_file = "/etc/chainflip/keys/signing_key_file"

[eth]
# Ethereum RPC endpoints (websocket and http for redundancy).
ws_node_endpoint = "YOUR_WSS_ENDPOINT"
http_node_endpoint = "YOUR_HTTPS_ENDPOINT"

# Ethereum private key file path. This file should contain a hex-encoded private key.
private_key_file = "/etc/chainflip/keys/ethereum_key_file"

[signing]
db_file = "/etc/chainflip/data.db"
```
**replace:**
- `YOUR_IP_ADDRESS`   : your VPS IP
- `YOUR_WSS_ENDPOINT` : your goerli wss endpoint from Alchemy/Infura
- `YOUR_WSS_ENDPOINT` : your goerli https endpoint from Alchemy/Infura

**Save file**
`Ctrl + X` and press `Y` then `Enter`

# 6. Start Node

### a. start
```
sudo systemctl start chainflip-node
```
### b. check status
```
sudo systemctl status chainflip-node
```

output will be like this:

![chengflip3](https://user-images.githubusercontent.com/78480857/203667584-131bd4a4-a76c-4a0b-aa98-2ad9bcc1697b.png)

exit
`Ctrl + C`

### c. check logs
```
tail -f /var/log/chainflip-node.log
```
**MAKE SURE YOUR NODE IS SYNCED WITH LATEST BLOCK BEFORE CONTIINUE TO THE NEXT STEP**

synced node output will be like:

![chengflip5](https://user-images.githubusercontent.com/78480857/203667598-9ebd0cdc-c01c-45d7-918f-3316e3d3d2fa.png)


# 7. Start Engine
### a. start engine
```
sudo systemctl start chainflip-engine
```

### b. check status
```
sudo systemctl status chainflip-engine
```

make sure its active, output will be like:

![chengflip6](https://user-images.githubusercontent.com/78480857/203667608-361635c1-db7a-4f91-878d-8d4c8045db58.png)


# 8. Enable Node & Engine

### a. enable Node
```
sudo systemctl enable chainflip-node
```

### b. enable Engine
```
sudo systemctl enable chainflip-engine
```


# 9. Stake

- Visit Web dashboard https://stake-perseverance.chainflip.io/
- Connect your Metamask, use your wallet that used before
- Click My Node
- +Add Node
- Input your public key(SS58) in step 4.d before.
- Stake your FLIP Token for your Node

![chengflep1](https://user-images.githubusercontent.com/78480857/203676886-628a355e-8832-44a3-992c-73f1f6a4bb92.png)


# 10. Register & Activate

### a. register
**Make sure your staked FLIP token confirmed and visible on the website before Register**
```
sudo chainflip-cli \
      --config-path /etc/chainflip/config/Default.toml \
      register-account-role Validator
```
press `Y` then `Enter`

### b. activate
```
sudo chainflip-cli \
    --config-path /etc/chainflip/config/Default.toml \
    activate
```

### c. join rotate for next auction
```
sudo chainflip-cli \
    --config-path /etc/chainflip/config/Default.toml rotate
```

### d. change your Node name
```
sudo chainflip-cli \
    --config-path /etc/chainflip/config/Default.toml \
    vanity-name <YOUR_NODE_NAME>
```

*replace `<YOUR_NODE_NAME>` with your Node name


**All Set!**

you can check your Node status on [Chainflip Website](https://stake-perseverance.chainflip.io/)
