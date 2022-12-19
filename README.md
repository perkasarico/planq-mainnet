<p align="center">
  <img width="270" height="auto" src="https://user-images.githubusercontent.com/108969749/208448900-1cd072da-d0cd-4d3b-b24c-11ae9db0ac76.png">
</p>



### Spesifikasi Hardware :
NODE  | CPU     | RAM      | SSD     |
| ------------- | ------------- | ------------- | -------- |
| Mainnet | 4          | 32         | 1TB  |

### Install otomatis
```
wget -O planq.sh https://raw.githubusercontent.com/Whalealert/planq-mainnet/main/planq.sh && chmod +x planq.sh && ./planq.sh
```
### Load variable ke system
```
source $HOME/.bash_profile
```
### Statesync
```
N/A
```
### Informasi node

   * cek sync node
```
planqd status 2>&1 | jq .SyncInfo
```
   * cek log node
```
journalctl -fu planqd -o cat
```
   * cek node info
```
planqd status 2>&1 | jq .NodeInfo
```
   * cek validator info
```
planqd status 2>&1 | jq .ValidatorInfo
```
  * cek node id
```
planqd tendermint show-node-id
```

### Membuat wallet
   * wallet baru
```
planqd keys add $WALLET
```
   * recover wallet
```
planqd keys add $WALLET --recover
```
   * list wallet
```
planqd keys list
```
   * hapus wallet
```
planqd keys delete $WALLET
```
### Simpan informasi wallet
```
PLANQ_WALLET_ADDRESS=$(planqd keys show $WALLET -a)
PLANQ_VALOPER_ADDRESS=$(planqd keys show $WALLET --bech val -a)
echo 'export PLANQ_WALLET_ADDRESS='${PLANQ_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export PLANQ_VALOPER_ADDRESS='${PLANQ_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```

### Membuat validator
 * cek balance
```
planqd query bank balances $PLANQ_WALLET_ADDRESS
```
 * membuat validator
```
planqd tx staking create-validator \
  --amount=1000000000000aplanq \
  --pubkey=$(planqd tendermint show-validator) \
  --moniker=$NODENAME \
  --chain-id=planq_7070-2 \
  --commission-rate="0.05" \
  --commission-max-rate="0.20" \
  --commission-max-change-rate="0.01" \
  --min-self-delegation="1000000" \
  --gas="1000000" \
  --gas-prices="30000000000aplanq" \
  --gas-adjustment="1.15" \
  --form=$WALLET \
  --chain-id=$PLANQ_CHAIN_ID
  
```
 * edit validator
```
planqd tx staking edit-validator \
  --moniker="nama-node" \
  --identity="<your_keybase_id>" \
  --website="<your_website>" \
  --details="<your_validator_description>" \
  --chain-id=PLANQ_CHAIN_ID \
  --fees 250aplanq \
  --from=$WALLET
```
 ° unjail validator
```
planqd tx slashing unjail \
  --broadcast-mode=block \
  --from=$WALLET \
  --chain-id=$PLAN_CHAIN_ID \
  --fees=250aplanq
```
### Voting
```
planqd tx gov vote 1 yes --from $WALLET --chain-id=$PLANQ_CHAIN_ID --fees=250umis
```
### Delegasi dan Rewards
  * delegasi
```
planqd tx staking delegate $PLANQ_VALOPER_ADDRESS 1000000umis --from=$WALLET --chain-id=$PLANQ_CHAIN_ID --fees=250aplanq
```
  * withdraw reward
```
planqd tx distribution withdraw-all-rewards --from=$WALLET --chain-id=$PLANQ_CHAIN_ID --fees=250aplanq
```
  * withdraw reward beserta komisi
```
planqd tx distribution withdraw-rewards $PLANQ_VALOPER_ADDRESS --from=$WALLET --commission --chain-id=$PLANQ_CHAIN_ID --fees=250aplanq
```

### Hapus node
```
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

