# Cardano Stake Pool

## Key Generate

```bash
docker compose run --rm preprod-cardano-cli-tools -c \
    'cardano-cli address key-gen \
    --verification-key-file /keys/pool_keys/payment.vkey \
    --signing-key-file /keys/pool_keys/payment.skey'

# 生成質押密鑰
docker compose run --rm preprod-cardano-cli-tools -c \
    'cardano-cli conway stake-address key-gen \
    --verification-key-file /keys/pool_keys/stake.vkey \
    --signing-key-file /keys/pool_keys/stake.skey'

# 冷密鑰對
docker compose run --rm preprod-cardano-cli-tools -c \
    'cardano-cli node key-gen \
    --cold-verification-key-file /keys/pool_keys/node.vkey \
    --cold-signing-key-file /keys/pool_keys/node.skey \
    --operational-certificate-issue-counter-file /keys/pool_keys/node.counter'

# KES 熱密鑰對 (Hot KES Keys) - 用於出塊簽名
docker compose run --rm preprod-cardano-cli-tools -c \
    'cardano-cli node key-gen-KES \
    --verification-key-file /keys/pool_keys/hot.vkey \
    --signing-key-file /keys/pool_keys/hot.skey'

# VRF 密鑰對 (VRF Keys) - 用於出塊隨機性選舉
docker compose run --rm preprod-cardano-cli-tools -c \
    'cardano-cli node key-gen-VRF \
    --verification-key-file /keys/pool_keys/vrf.vkey \
    --signing-key-file /keys/pool_keys/vrf.skey'
```

用質押密鑰生成質押礦池地址

```bash
docker compose run --rm preprod-cardano-cli-tools -c \
    'cardano-cli address build \
    --payment-verification-key-file /keys/pool_keys/payment.vkey \
    --stake-verification-key-file /keys/pool_keys/stake.vkey \
    --testnet-magic 1 \
    --out-file /keys/pool_keys/payment.addr'
```

輸出質押礦池地址

```bash
docker compose run --rm preprod-cardano-cli-tools -c \
    'cardano-cli conway stake-address build \
    --stake-verification-key-file /keys/pool_keys/stake.vkey \
    --testnet-magic 1 \
    --out-file /keys/pool_keys/stake.addr'
```

生成發行操作證明

檢查 kes-period

```bash
# 查詢鏈尖資訊 (Tip)
# Preview Network 的 Network Magic 是 2
docker compose run --rm preprod-cardano-cli-tools -c 'cardano-cli query tip --testnet-magic 1 --socket-path /ipc-pub/node.socket'
{
    "block": 4156083,
    "epoch": 254,
    "era": "Conway",
    "hash": "dd5d3a4d036b0d3be235925d1b6b81f85a3cd180872851a6764d69efe416f6af",
    "slot": 108191000,
    "slotInEpoch": 104600,
    "slotsToEpochEnd": 327400,
    "syncProgress": "100.00"
}
```

當前 KES 週期 Current KES Period = 108190383/129600 = 834

--kes-period = 834 + 1 = 835

```bash
docker compose run --rm preprod-cardano-cli-tools -c \
    'cardano-cli node issue-op-cert \
    --kes-verification-key-file /keys/pool_keys/hot.vkey \
    --cold-signing-key-file /keys/pool_keys/node.skey \
    --operational-certificate-issue-counter-file /keys/pool_keys/node.counter \
    --kes-period 835 \
    --out-file /keys/pool_keys/node.cert'
```

## Get Test Water

```bash
sudo cat pool_keys/payment.addr
addr_test1qpcqncfxkh26z5z7k49upccue44jn2ep8kw8qd63tzgymnt2pygh5g5wh2uvlsclqz0kc44y5xsqp6cypg6fqhj9gwrskmhwvv
```

Check Stake Pool

```bash
docker compose run --rm preprod-cardano-cli-tools -c \
    'cardano-cli query utxo \
    --address $(cat /keys/pool_keys/payment.addr) \
    --testnet-magic 1 \
    --socket-path /ipc-pub/node.socket'
```

## 取得餘額之後註冊礦池準備

生成質押密鑰註冊憑證

```bash
docker compose run --rm preprod-cardano-cli-tools -c \
    'cardano-cli conway stake-address registration-certificate \
    --stake-verification-key-file /keys/pool_keys/stake.vkey \
    --key-reg-deposit-amt 2000000 \
    --out-file /keys/pool_keys/stake.cert'
```

poolMetadata.json 的 HASH

```bash
docker compose run --rm preprod-cardano-cli-tools -c \
    "curl -ksL https://ironminer.pages.dev/mki/poolMetadata.json | \
    cardano-cli conway stake-pool metadata-hash --pool-metadata-file /dev/stdin"
d3ac9f2ca663a75e80c021c7020837485b75dd7036916b312dec296f02b5da8f
```

生成質押池註冊憑證

metadata.json 是質押礦池的 Metadata 檔案

```bash
docker compose run --rm preprod-cardano-cli-tools -c \
    'cardano-cli conway stake-pool registration-certificate \
    --cold-verification-key-file /keys/pool_keys/node.vkey \
    --vrf-verification-key-file /keys/pool_keys/vrf.vkey \
    --pool-pledge 500000000 \
    --pool-cost 320000000 \
    --pool-margin 0 \
    --pool-reward-account-verification-key-file /keys/pool_keys/stake.vkey \
    --pool-owner-stake-verification-key-file /keys/pool_keys/stake.vkey \
    --pool-relay-ipv4 "127.0.0.1" \
    --pool-relay-port 5999 \
    --metadata-url "https://ironminer.pages.dev/mki/poolMetadata.json" \
    --metadata-hash d3ac9f2ca663a75e80c021c7020837485b75dd7036916b312dec296f02b5da8f \
    --testnet-magic 1 \
    --out-file /keys/pool_keys/pool.cert'
```

## 註冊質押池

取得 Payment Addr 的 UTXO

```bash
sudo cat pool_keys/payment.addr
docker compose run --rm preprod-cardano-cli-tools -c \
    'cardano-cli conway query utxo \
    --address addr_test1qpcqncfxkh26z5z7k49upccue44jn2ep8kw8qd63tzgymnt2pygh5g5wh2uvlsclqz0kc44y5xsqp6cypg6fqhj9gwrskmhwvv \
    --testnet-magic 1 \
    --socket-path /ipc-pub/node.socket'
{
    "925747d77e17a9ab0a945a03f90f5efd1c618e0252c6db6d326bb5bce59955c2#0": {
        "address": "addr_test1qpcqncfxkh26z5z7k49upccue44jn2ep8kw8qd63tzgymnt2pygh5g5wh2uvlsclqz0kc44y5xsqp6cypg6fqhj9gwrskmhwvv",
        "datum": null,
        "datumhash": null,
        "inlineDatum": null,
        "inlineDatumRaw": null,
        "referenceScript": null,
        "value": {
            "lovelace": 10000000000
        }
    }
}
```

獲取協議參數

```bash
docker compose run --rm preprod-cardano-cli-tools -c \
    'cardano-cli query protocol-parameters \
    --testnet-magic 1 \
    --socket-path /ipc-pub/node.socket \
    --out-file /keys/pool_keys/protocol.json'
```

建立交易草稿 (Build Raw Transaction)

```bash
TX_HASH_IX0=$(docker compose run --rm preprod-cardano-cli-tools -c 'cardano-cli conway query utxo --address $(cat /keys/pool_keys/payment.addr) --testnet-magic 1 --socket-path /ipc-pub/node.socket' | jq -r "keys | .[0]") &&
PAYMENT_ADDR=$(sudo cat pool_keys/payment.addr) &&
TTL_SLOT=$(docker compose run --rm preprod-cardano-cli-tools -c 'cardano-cli query tip --testnet-magic 1 --socket-path /ipc-pub/node.socket' | grep -E "^\s*\"slot\":" | awk '{print $2}' | sed 's/,//') &&
TTL_BUFFER=3600 &&
TTL_SLOT_NEW=$((TTL_SLOT + TTL_BUFFER)) &&
echo $TX_HASH_IX0 &&
echo $PAYMENT_ADDR &&
echo $TTL_SLOT &&
echo $TTL_BUFFER &&
echo $TTL_SLOT_NEW

docker compose run --rm preprod-cardano-cli-tools -c \
    "cardano-cli conway transaction build-raw \
    --tx-in $TX_HASH_IX0 \
    --tx-out $PAYMENT_ADDR+0 \
    --certificate-file /keys/pool_keys/stake.cert \
    --certificate-file /keys/pool_keys/pool.cert \
    --fee 0 \
    --out-file /keys/pool_keys/tx.draf \
    --invalid-hereafter $TTL_SLOT_NEW"
```

計算交易費用有可能會是錯的，以回應為主

```bash
docker compose run --rm preprod-cardano-cli-tools -c \
    "cardano-cli conway transaction calculate-min-fee \
    --tx-body-file /keys/pool_keys/tx.draf \
    --tx-in-count 1 \
    --tx-out-count 1 \
    --witness-count 3 \
    --testnet-magic 1 \
    --protocol-params-file /keys/pool_keys/protocol.json \
    --out-file /keys/pool_keys/fee.txt"
{
    "fee": 186885
}
```

```bash
# - 502000000 因為還有註冊礦池的抵押費
#根據 Cardano Preprod 網路的標準參數和你的交易金額，我們可以推測多出來的 $502$ ADA 是這樣組成的：
#礦池押金 Deposit Pool： 500 ADA 標準值
#質押地址押金 Deposit Stake： 2 ADA (標準值)
#交易手續費 (TxFee): 通常是 $0.1$ 到 $0.3$ ADA 之間。
TX_HASH_IX0=$(docker compose run --rm preprod-cardano-cli-tools -c 'cardano-cli conway query utxo --address $(cat /keys/pool_keys/payment.addr) --testnet-magic 1 --socket-path /ipc-pub/node.socket' | jq -r "keys | .[0]") &&
AMOUNT_LOVELACE=$(docker compose run --rm preprod-cardano-cli-tools -c 'cardano-cli conway query utxo --address $(cat /keys/pool_keys/payment.addr) --testnet-magic 1 --socket-path /ipc-pub/node.socket' | jq -r "map(.value.lovelace) | add") &&
FINAL_FEE=187237 &&
CHANGE_AMOUNT=$((AMOUNT_LOVELACE - FINAL_FEE - 502000000)) &&
PAYMENT_ADDR=$(sudo cat pool_keys/payment.addr) &&
TTL_SLOT=$(docker compose run --rm preprod-cardano-cli-tools -c 'cardano-cli query tip --testnet-magic 1 --socket-path /ipc-pub/node.socket' | grep -E "^\s*\"slot\":" | awk '{print $2}' | sed 's/,//') &&
TTL_BUFFER=3000 &&
TTL_SLOT_NEW=$((TTL_SLOT + TTL_BUFFER)) &&
echo $AMOUNT_LOVELACE &&
echo $FINAL_FEE &&
echo $CHANGE_AMOUNT &&
echo $PAYMENT_ADDR &&
echo $TTL_SLOT &&
echo $TTL_BUFFER &&
echo $TTL_SLOT_NEW

docker compose run --rm preprod-cardano-cli-tools -c \
    "cardano-cli conway transaction build-raw \
    --tx-in $TX_HASH_IX0 \
    --tx-out $PAYMENT_ADDR+$CHANGE_AMOUNT \
    --certificate-file /keys/pool_keys/stake.cert \
    --certificate-file /keys/pool_keys/pool.cert \
    --fee $FINAL_FEE \
    --out-file /keys/pool_keys/tx.body \
    --invalid-hereafter $TTL_SLOT_NEW"
```

簽署交易

這一步需要使用兩個簽名密鑰：

payment.skey (支付密鑰：用於花費 UTXO 輸入和找零)

node.skey (冷密鑰：用於簽署質押池註冊憑證)

```bash
docker compose run --rm preprod-cardano-cli-tools -c \
    "cardano-cli conway transaction sign \
    --tx-body-file /keys/pool_keys/tx.body \
    --signing-key-file /keys/pool_keys/payment.skey \
    --signing-key-file /keys/pool_keys/node.skey \
    --signing-key-file /keys/pool_keys/stake.skey \
    --testnet-magic 1 \
    --out-file /keys/pool_keys/tx.signed"
```

提交交易，這是最終將質押池註冊資訊上鏈的動作

```bash
docker compose run --rm preprod-cardano-cli-tools -c \
    "cardano-cli conway transaction submit \
    --tx-file /keys/pool_keys/tx.signed \
    --testnet-magic 1 \
    --socket-path /ipc-pub/node.socket"
{"txhash":"694b74dcd45f50d3086bd717324730776dbe90550ee0d7521db695a08ede1d87"}
```

## 質押池註冊結果

這是公開識別質押池的唯一 ID。可以使用您的冷密鑰 (node.vkey) 來計算它。

```bash
docker compose run --rm preprod-cardano-cli-tools -c \
    'cardano-cli conway stake-pool id \
    --cold-verification-key-file /keys/pool_keys/node.vkey'
pool17uqz7e8e5e4096p4qpvfq7hev8zsysdwy3gc4txlqxf5vfcmnnc
```

驗證 VRF Key Hash - 確認鑄塊憑證

```bash
docker compose run --rm preprod-cardano-cli-tools -c \
    "cardano-cli conway node key-hash-VRF \
    --verification-key-file /keys/pool_keys/vrf.vkey"
4ca2305b67e769d3e475509eaf3443bd218d237abf6349089e2987f77b71cb74
```

## 激活礦池

把 Pool 的 Payment address 剩餘 ADA 質押到自己的礦池

```bash
docker compose run --rm preprod-cardano-cli-tools -c \
    'cardano-cli conway stake-address stake-delegation-certificate \
    --stake-verification-key-file /keys/pool_keys/stake.vkey \
    --stake-pool-id pool17uqz7e8e5e4096p4qpvfq7hev8zsysdwy3gc4txlqxf5vfcmnnc \
    --out-file /keys/pool_keys/delegation.cert'
```

取得 Payment Addr 的 UTXO

```bash
sudo cat pool_keys/payment.addr
docker compose run --rm preprod-cardano-cli-tools -c \
    'cardano-cli conway query utxo \
    --address addr_test1qpcqncfxkh26z5z7k49upccue44jn2ep8kw8qd63tzgymnt2pygh5g5wh2uvlsclqz0kc44y5xsqp6cypg6fqhj9gwrskmhwvv \
    --testnet-magic 1 \
    --socket-path /ipc-pub/node.socket'
{
    "694b74dcd45f50d3086bd717324730776dbe90550ee0d7521db695a08ede1d87#0": {
        "address": "addr_test1qpcqncfxkh26z5z7k49upccue44jn2ep8kw8qd63tzgymnt2pygh5g5wh2uvlsclqz0kc44y5xsqp6cypg6fqhj9gwrskmhwvv",
        "datum": null,
        "datumhash": null,
        "inlineDatum": null,
        "inlineDatumRaw": null,
        "referenceScript": null,
        "value": {
            "lovelace": 9497812763
        }
    }
}
```

建立委託交易草稿 (tx-deleg.draft)

```bash
TX_HASH_IX0=$(docker compose run --rm preprod-cardano-cli-tools -c 'cardano-cli conway query utxo --address $(cat /keys/pool_keys/payment.addr) --testnet-magic 1 --socket-path /ipc-pub/node.socket' | jq -r "keys | .[0]") &&
PAYMENT_ADDR=$(sudo cat pool_keys/payment.addr) &&
TTL_SLOT=$(docker compose run --rm preprod-cardano-cli-tools -c 'cardano-cli query tip --testnet-magic 1 --socket-path /ipc-pub/node.socket' | grep -E "^\s*\"slot\":" | awk '{print $2}' | sed 's/,//') &&
TTL_BUFFER=3000 &&
TTL_SLOT_NEW=$((TTL_SLOT + TTL_BUFFER)) &&
echo $PAYMENT_ADDR &&
echo $TTL_SLOT &&
echo $TTL_BUFFER &&
echo $TTL_SLOT_NEW

docker compose run --rm preprod-cardano-cli-tools -c \
    "cardano-cli conway transaction build-raw \
    --tx-in $TX_HASH_IX0 \
    --tx-out $PAYMENT_ADDR+0 \
    --certificate-file /keys/pool_keys/delegation.cert \
    --fee 0 \
    --out-file /keys/pool_keys/tx-deleg.draft \
    --invalid-hereafter $TTL_SLOT_NEW"
```

計算交易費用有可能會是錯的，以回應為主

witness-count 2 是因為簽名的時候需要幾個 key

```bash
docker compose run --rm preprod-cardano-cli-tools -c \
    "cardano-cli conway transaction calculate-min-fee \
    --tx-body-file /keys/pool_keys/tx-deleg.draft \
    --tx-in-count 1 \
    --tx-out-count 1 \
    --witness-count 2 \
    --testnet-magic 1 \
    --protocol-params-file /keys/pool_keys/protocol.json \
    --out-file /keys/pool_keys/fee-deleg.txt"
{
    "fee": 186885
}
```

建立最終委託交易

```bash
TX_HASH_IX0=$(docker compose run --rm preprod-cardano-cli-tools -c 'cardano-cli conway query utxo --address $(cat /keys/pool_keys/payment.addr) --testnet-magic 1 --socket-path /ipc-pub/node.socket' | jq -r "keys | .[0]") &&
AMOUNT_LOVELACE=$(docker compose run --rm preprod-cardano-cli-tools -c 'cardano-cli conway query utxo --address $(cat /keys/pool_keys/payment.addr) --testnet-magic 1 --socket-path /ipc-pub/node.socket' | jq -r "map(.value.lovelace) | add") &&
FINAL_FEE=186885 &&
CHANGE_AMOUNT=$((AMOUNT_LOVELACE - $FINAL_FEE)) &&
PAYMENT_ADDR=$(sudo cat pool_keys/payment.addr) &&
TTL_SLOT=$(docker compose run --rm preprod-cardano-cli-tools -c 'cardano-cli query tip --testnet-magic 1 --socket-path /ipc-pub/node.socket' | grep -E "^\s*\"slot\":" | awk '{print $2}' | sed 's/,//') &&
TTL_BUFFER=3000 &&
TTL_SLOT_NEW=$((TTL_SLOT + TTL_BUFFER)) &&
echo $AMOUNT_LOVELACE &&
echo $FINAL_FEE &&
echo $CHANGE_AMOUNT &&
echo $PAYMENT_ADDR &&
echo $TTL_SLOT &&
echo $TTL_BUFFER &&
echo $TTL_SLOT_NEW

docker compose run --rm preprod-cardano-cli-tools -c \
    "cardano-cli conway transaction build-raw \
    --tx-in $TX_HASH_IX0 \
    --tx-out $PAYMENT_ADDR+$CHANGE_AMOUNT \
    --certificate-file /keys/pool_keys/delegation.cert \
    --fee $FINAL_FEE \
    --out-file /keys/pool_keys/tx-deleg.body \
    --invalid-hereafter $TTL_SLOT_NEW"
```

簽署交易

```bash
docker compose run --rm preprod-cardano-cli-tools -c \
    "cardano-cli conway transaction sign \
    --tx-body-file /keys/pool_keys/tx-deleg.body \
    --signing-key-file /keys/pool_keys/payment.skey \
    --signing-key-file /keys/pool_keys/stake.skey \
    --testnet-magic 1 \
    --out-file /keys/pool_keys/tx-deleg.signed"
```

提交交易

```bash
docker compose run --rm preprod-cardano-cli-tools -c \
    "cardano-cli conway transaction submit \
    --tx-file /keys/pool_keys/tx-deleg.signed \
    --testnet-magic 1 \
    --socket-path /ipc-pub/node.socket"
{"txhash":"e253dc08db1884c276369a8896a6e8067a606577ebe4ef1702b38d5c197214dd"}
```

檢查是不是質押成功

```bash
docker compose run --rm preprod-cardano-cli-tools -c \
    'cardano-cli query stake-address-info \
    --address $(cat /keys/pool_keys/stake.addr) \
    --testnet-magic 1 \
    --socket-path /ipc-pub/node.socket'
[
    {
        "address": "stake_test1up4qjyt6y28t4wx0cv0sp8mv26j2rgqqavzq5dystez58pcsszdxq",
        "govActionDeposits": {},
        "rewardAccountBalance": 0,
        "stakeDelegation": "pool17uqz7e8e5e4096p4qpvfq7hev8zsysdwy3gc4txlqxf5vfcmnnc",
        "stakeRegistrationDeposit": 2000000,
        "voteDelegation": null
    }
]
```

## 檢查

## 檢查出塊狀況

要等兩個 epoch 才會檢查的到

```bash
docker compose run --rm preprod-cardano-cli-tools -c \
    "cardano-cli conway query stake-snapshot \
    --stake-pool-id pool17uqz7e8e5e4096p4qpvfq7hev8zsysdwy3gc4txlqxf5vfcmnnc \
    --testnet-magic 1 \
    --socket-path /ipc-bp/node.socket"

docker compose run --rm preprod-cardano-cli-tools -c \
    "cardano-cli conway query leadership-schedule \
    --testnet-magic 1 \
    --socket-path /ipc-pub/node.socket \
    --genesis /opt/cardano/config/preprod/shelley-genesis.json \
    --cold-verification-key-file /keys/pool_keys/node.vkey \
    --vrf-signing-key-file /keys/pool_keys/vrf.skey \
    --current"
```

```bash
docker compose logs preprod-cardano-node-bp | grep "Forged"
docker compose logs preprod-cardano-node-bp | grep "TraceForgedBlock"
docker compose logs preprod-cardano-node-bp | grep "SlotForged"

```bash
docker compose run --rm preprod-cardano-cli-tools -c \
    'cardano-cli query tip --testnet-magic 1 --socket-path /ipc-bp/node.socket'
{
    "block": 4155857,
    "epoch": 254,
    "era": "Conway",
    "hash": "cb04637f9a86aa9e88be557e614fe40267ea7716dfab6053d33a862bb4c4d987",
    "slot": 108185928,
    "slotInEpoch": 99528,
    "slotsToEpochEnd": 332472,
    "syncProgress": "99.98"
docker compose run --rm preprod-cardano-cli-tools -c \
    'cardano-cli query tip --testnet-magic 1 --socket-path /ipc-pub/node.socket'
{
    "block": 4156632,
    "epoch": 254,
    "era": "Conway",
    "hash": "44630a2630fee950d94ef4658b22dbf46ca74501629bb3bfe942f34aaf9fd2d0",
    "slot": 108204725,
    "slotInEpoch": 118325,
    "slotsToEpochEnd": 313675,
    "syncProgress": "100.00"
}
docker compose run --rm preprod-cardano-cli-tools -c \
    'cardano-cli query tip --testnet-magic 1 --socket-path /ipc-priv/node.socket'
{
    "block": 4156535,
    "epoch": 254,
    "era": "Conway",
    "hash": "859b6411b9932106761cc8446c7da36c78a7cd5f822f7708ce1ddb0e2a28dca6",
    "slot": 108202391,
    "slotInEpoch": 115991,
    "slotsToEpochEnd": 316009,
    "syncProgress": "100.00"
}
```

### 錯誤訊息

```log
[preprod-:cardano.node.PeerSelection:Warning:85] [2025-11-23 15:32:23.13 UTC] TraceDemoteLocalAsynchronous (fromList [(172.31.0.11:6001,(PeerCold,Just RepromoteDelay 13.068097382129s))])
```

將 Private Relay 的 ProtocolIdleTimeout 設置為 30 秒

```bash
// 在 priv-relay1-config.json 中
{
  // ... 其他配置
  "ProtocolIdleTimeout": 30, // 增加到 30 秒
  // ...
}
```

## 維護步驟

### 檢查

```bash
docker compose run --rm -ti preprod-cardano-cli-tools -c "sleep 3600"
```

### 無法同步鏈上資料

必須要先執行 docker compose stop，假如 Public Relay 是 100% 同步，可以依順序將 Public Relay 的 DB 複製到 Private Relay，然後重新啟動 Private Relay，再重複一次到 BP

### 需要週期性的替換 node.cert

當您的 KES 密鑰即將過期時。

1. KES 密鑰的有效期 (有效期約 90 天)

- 您的 node.cert (當前): 起始於 KES 週期 46。
- 有效期： Cardano 預設配置的 KES 密鑰可持續 90 個 KES 週期。
- 您的憑證過期週期： 起始週期 + 有效期 - 1 = 46 + 90 - 1 = 135
- 您的憑證將在 KES 週期 136 開始時失效。

| 密鑰類型 | 職責 | 輪換頻率 |
| :--- | :--- | :--- |
| `node.skey` / `node.vkey` (冷密鑰) | 質押池的永久身份。用於簽署質押池註冊交易、質押池退役交易，以及簽發操作證明 (`node.cert`)。 | 很少。除非您的密鑰洩露，否則不應該更換。 |
| `hot.skey` / `hot.vkey` (熱密鑰 KES Key) | 節點的臨時運營密鑰。用於日常出塊簽名。 | 定期。每 90 個 KES 週期 (約 135 天) 需要更換。 |
| `node.cert` (操作證明) | 將冷密鑰和熱密鑰綁定，證明熱密鑰有權利為該質押池出塊。 | 定期。與熱密鑰一起更換。 |

2. KES 週期與實際時間的換算

| 單位數值 (Preview Testnet) | 換算 (Slots) | 換算 (時間) |
| :--- | :--- | :--- |
| 1 個 KES 週期 | 129,600 Slots | 129,600 秒 ≈ 1.5 天 |
| 90 個 KES 週期 | 90 x 129,600 Slots | 11,664,000 秒 ≈ 135 天 |

結論： 您的操作證明（node.cert）大約有 135 天的壽命。

3. 輪換的最佳時機您應該在 KES 密鑰過期前 2 ~ 3 個週期（即 3 到 4.5 天前）就執行輪換。輪換過程有兩步：步驟 A: 生成新的 KES 密鑰對 (hot.skey / hot.vkey)您必須先丟棄舊的熱密鑰，生成一對全新的 KES 密鑰對。
4. 刪除舊的熱密鑰 (這是唯一需要被替換的密鑰)`rm /keys/pool_keys/hot.skey /keys/pool_keys/hot.vkey`

### 檢查當前熱密鑰對何時過期

```bash
docker compose exec preprod-cardano-node-bp cardano-cli query kes-period-info \
    --op-cert-file /keys/pool_keys/node.cert \
    --testnet-magic 1 \
    --socket-path /ipc/node.socket
✓ Operational certificate's KES period is within the correct KES period interval
✓ The operational certificate counter agrees with the node protocol state counter
{
    "qKesCurrentKesPeriod": 842,
    "qKesEndKesInterval": 897,
    "qKesKesKeyExpiry": "2026-02-24T12:00:00Z",
    "qKesMaxKESEvolutions": 62,
    "qKesNodeStateOperationalCertificateNumber": 0,
    "qKesOnDiskOperationalCertificateNumber": 0,
    "qKesRemainingSlotsInKesPeriod": 7020658,
    "qKesSlotsPerKesPeriod": 129600,
    "qKesStartKesInterval": 835
}
```

備份

```bash
sudo cp ./pool_keys/hot.skey ./pool_keys/hot.skey.OLD
sudo cp ./pool_keys/node.cert ./pool_keys/node.cert.OLD
```

### 生成新的 KES 熱密鑰對

```bash
docker compose exec preprod-cardano-node-bp cardano-cli node key-gen-KES \
    --verification-key-file /keys/pool_keys/hot.vkey \
    --signing-key-file /keys/pool_keys/hot.skey
```

檢查 kes period

```bash
docker compose run --rm preprod-cardano-cli-tools -c 'cardano-cli query tip --testnet-magic 1 --socket-path /ipc-pub/node.socket'
{
    "block": 4197384,
    "epoch": 256,
    "era": "Conway",
    "hash": "c4aabc1e8f7f086870785b2e6c4e245a9971456eb1e2fb9207004b0afaef5865",
    "slot": 109231074,
    "slotInEpoch": 280674,
    "slotsToEpochEnd": 151326,
    "syncProgress": "100.00"
}
```

當前 KES 週期 Current KES Period = 109231074/129600 = 842

使用新的 KES 密鑰重新發行操作證明使用您新生成的 hot.vkey 和當時的最新 KES 週期來發行操作證明。

```bash
# 先查詢最新的 KES 週期，假設為 133
docker compose exec preprod-cardano-node-bp cardano-cli node issue-op-cert \
    --kes-verification-key-file /keys/pool_keys/hot.vkey \
    --cold-signing-key-file /keys/pool_keys/node.skey \
    --operational-certificate-issue-counter-file /keys/pool_keys/node.counter \
    --kes-period 842 \
    --out-file /keys/pool_keys/node.cert
```

重新啟動 BP 之後，檢查是不是延長了 key period

## 備份

```bash
tar -czvf preprod-cardano-node-20251219.tgz config docker-compose-multi.yml docker-compose-single.yml docker-compose.yml node-db-pub-relay1 pool_keys start-node-bp.sh start-node-priv-relay.sh start-node-pub-relay.sh start-node-single.sh

scp -3 preview-cardano-node.tar.gz k8s-w-01:~/preview-cardano-node.tar.gz
```
