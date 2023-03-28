
# PLANQ - NODE CONFIGURE

Follow this step to config your node.


## System Update
```bash
sudo apt update && sudo apt upgrade -y
```
## Auto Installation

```bash
wget -O planq.sh https://raw.githubusercontent.com/Alfonova-Node/NODE_TESTNET/main/PLANQ/planq.sh && chmod +x planq.sh && ./planq.sh
```

## Load Variable

```bash
source $HOME/.bash_profile
```

## Snapshot

```bash
sudo systemctl stop planqd
cp $HOME/.planqd/data/priv_validator_state.json $HOME/.planqd/priv_validator_state.json.backup
rm -rf $HOME/.planqd/data

curl -L https://snapshot-planq.alfonova.online/planq/planq-snapshot-20230218.tar.lz4  | lz4 -dc - | tar -xf - -C $HOME/.planqd
mv $HOME/.planqd/priv_validator_state.json.backup $HOME/.planqd/data/priv_validator_state.json

sudo systemctl restart planqd && journalctl -u planqd -f --no-hostname -o cat
```

## Node Information
- Show your sync node
```bash
planqd status 2>&1 | jq .SyncInfo
```
- Show your log node
```bash
journalctl -fu planqd -o cat
```
- Check your node information
```bash
planqd status 2>&1 | jq .NodeInfo
```
- Check your validator information
```bash
planqd status 2>&1 | jq .ValidatorInfo
```
- Check your node ID
```bash
planqd tendermint show-node-id
```

## Wallet
- Create wallet
```bash
planqd keys add $WALLET
```
- Recover wallet
```bash
planqd keys add $WALLET --recover
```
- View list wallet
```bash
planqd keys list
```
- Delete wallet
```bash
planqd keys delete $WALLET
```

## Save your information wallet
```bash
PLANQ_WALLET_ADDRESS=$(planqd keys show $WALLET -a)
```
```bash
PLANQ_VALOPER_ADDRESS=$(planqd keys show $WALLET --bech val -a)
```
```bash
echo 'export PLANQ_WALLET_ADDRESS='${PLANQ_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export PLANQ_VALOPER_ADDRESS='${PLANQ_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

## Create Validator
```bash
planqd tx staking create-validator \
  --amount=1000000000000aplanq \
  --pubkey=$(planqd tendermint show-validator) \
  --moniker=$NODENAME \
  --chain-id=planq_7070-2 \
  --commission-rate="0.05" \
  --commission-max-rate="0.20" \
  --commission-max-change-rate="0.05" \
  --min-self-delegation="1000000" \
  --gas="1000000" \
  --gas-prices="30000000000aplanq" \
  --gas-adjustment="1.15" \
  --from=$WALLET
```
## Edit Validator
```bash
planqd tx staking edit-validator \
  --new-moniker="nama-node" \
  --identity="<your_keybase_id>" \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=planq_7070-2 \
  --gas="1000000" \
  --gas-prices="30000000000aplanq" \
  --identity="" \
  --website="" \
  --details="t"
  --from=$WALLET
```
## Unjailed Validator
```bash
planqd tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=planq_7070-2 \
  --gas="1000000" \
  --gas-prices="30000000000aplanq"
```
## Check Balance
```bash
planqd query bank balances $PLANQ_WALLET_ADDRESS
```
## Delete Node
```bash
sudo systemctl stop planqd && \
sudo systemctl disable planqd && \
rm /etc/systemd/system/planqd.service && \
sudo systemctl daemon-reload && \
cd $HOME && \
rm -rf planq && \
rm -rf planq.sh && \
rm -rf .planqd && \
rm -rf $(which planqd)
```
## Source
https://github.com/Alfonova-Node/NODE_TESTNET/tree/main/PLANQ
