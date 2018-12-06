# Running a command line wallet

* After extracting the wallet binary to a folder, we need to initialize the wallet by executing the following command: 
``` sh
./beam-wallet --command init
```
* You will be prompted to provide wallet password and then seed (or `wallet_phrases`). If your node is running in a miner mode (mining threads > 0), the seed (phrases) should be the same as was set in the `--wallet_seed` (or `wallet_phrases`) flag of the node


# Printing wallet information

* To get the information about the current status of the wallet, execute:
``` sh
./beam-wallet --command info -n 127.0.0.1:10000
```


# Receiving Beams

* To receive beams start the wallet in a listening mode by running: 
``` sh
./beam-wallet --command listen -n 127.0.0.1:10000
```
* After entering the password, the wallet will print out the line similar to: `WalletID 4a0e54b24d5fdf06891a8eaa57b4b3ac16731e932a64da8ec768083495d624f1 subscribes to BBS channel 9`
* This shows the SBBS address the wallet is listening on. This address can be copied and sent to Sender.
* If you want to create new SBBS address use the following command: 
``` sh
./beam-wallet --command=new_addr --listen -n 127.0.0.1:10000
```

# Sending Beams

* To sending beams use the following command 
``` sh 
./beam-wallet --command=send -n 127.0.0.1:10000 -r 77de6bd3de40bc58ab7e4fb68d5e0596fd1e72f3c4fb3eb3d106082d89264909 -a 11.3 -f 0.2
```
* The send-related command line parameters of the wallet:

| Name | Description |
|------|-------------|
| `r` | SBBS address of the receiver node |
| `a` | amount of beams to send |
| `f` | transaction fee |


# The Full list of wallet command line options

## General options:
| Name | Description |
|------|-------------|
| `h` or `help` | list of all options |
| `p`  or `port arg (=10000)` | port to start the server on |
| `wallet_seed arg` | secret key generation seed |
| `wallet_phrases arg` | phrases to generate secret key according to BIP-39. `wallet_seed` option will be ignored |
| `log_level arg` | log level `[info|debug|verbose]` |
| `file_log_level arg` | file log level `[info|debug|verbose]` |
| `v` or `version` | return project version |
| `git_commit_hash` | return commit hash |

## Wallet options:

| Name | Description |
|------|-------------|
| `pass arg` | password for the wallet |
| `a` or `amount arg` | amount to send (in Beams, 1 Beam = 1000000 chattle) |
| `f` or `fee arg (=0)` | fee (in Beams, 1 Beam = 1000000 chattle) |
| `r` or `receiver_addr arg` | address of receiver |
| `n` or `node_addr arg` | address of node |
| `treasury_path arg (=treasury.mw)` | Block to create/append treasury to |
| `wallet_path arg (=wallet.db)` | path to wallet file |
| `bbs_keystore_path arg (=bbs_keys.db)` | path to file with bbs keys |
| `tx_history` | print transactions' history in info command |
| `tr_Count arg (=30)` | treasury UTXO count |
| `tr_HeightStep arg (=1440)` | treasury UTXO height lock step |
| `tr_BeamsPerUtxo arg (=10)` | treasury value of each UTXO (in Beams) |
| `command arg` | command to execute `[send|receive|listen|init|info|treasury]`

## Rules configuration:

| Name | Description |
|------|-------------|
| `CoinbaseEmission arg (=80000000)` | coinbase emission in a single block |
| `MaturityCoinbase arg (=60)`  | Number of blocks before coinbase UTXO can be spent |
| `MaturityStd arg (=0)` | Number of blocks before non-coinbase UTXO can be spent |
| `MaxBodySize arg (=1048576)` | Max block body size in `[bytes]` |
| `DesiredRate_s arg (=60)`| Desired rate of generated blocks in `[seconds]` |
| `DifficultyReviewCycle arg (=1440)` | Number of blocks after which the mining difficulty can be adjusted |
| `MaxDifficultyChange arg (=2)` | Max difficulty change after each cycle (each step is roughly x2 complexity) |
| `TimestampAheadThreshold_s arg (=7200)` | Block timestamp tolerance in `[seconds]` |
| `WindowForMedian arg (=25)` | How many blocks are considered in calculating the timestamp median |
| `AllowPublicUtxos arg (=0)` | Set to allow regular (non-coinbase) UTXO to have non-confidential signature |
| `FakePoW arg (=0)` | Don't verify PoW. Mining is simulated by the timer. For tests only. |

# List of Тestnet nodes

The Тestnet nodes can be used in the following scenarios:
* When specifying peers for the beam-node
* Where a wallet connects to the specific node

```
139.59.174.80:8100
167.99.221.104:8100
159.65.201.244:8100
142.93.217.246:8100
142.93.221.22:8100
178.62.59.65:8100
139.59.169.77:8100
167.99.166.33:8100
159.65.119.216:8100
142.93.221.66:8100
206.189.10.149:8100
138.68.134.136:8100
159.65.119.226:8100
142.93.221.80:8100
104.248.159.97:8100
159.65.101.94:8100
68.183.103.130:8100
142.93.213.21:8100
104.248.159.154:8100
188.166.165.240:8100
159.65.104.75:8100
142.93.213.106:8100
```

# Known limitations
* CPUs without SSE3 instruction set are not supported
