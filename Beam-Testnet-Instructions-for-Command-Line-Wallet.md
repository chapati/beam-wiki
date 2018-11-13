# Welcome to Beam Testnet 2

In case you encounter any problem, please email us at testnet@beam-mw.com or open a GitHub ticket at https://github.com/BeamMW/beam/issues

For effective investigation please attach the following items for every issue:
* Logs, compressed into a single archive
* Configuration file
* Command line parameters of the executed binary

# Running a command line wallet

* After extracting the wallet binary to a folder, we need to initialize the wallet by executing the following command: `./beam-wallet --command init`
* You will be prompted to provide wallet password and then seed (or `wallet_phrases`). If your node is running in a miner mode (mining threads > 0), the seed (phrases) should be the same as was set in the `--wallet_seed` (or `wallet_phrases`) flag of the node


## Printing wallet information

* To get the information about the current status of the wallet, execute: `./beam-wallet --command info -n 127.0.0.1:10000`


## Receiving Beams

* To receive beams start the wallet in a listening mode by running: `./beam-wallet --command listen -n 127.0.0.1:10000`
* After entering the password, the wallet will print out the line similar to: `WalletID 4a0e54b24d5fdf06891a8eaa57b4b3ac16731e932a64da8ec768083495d624f1 subscribes to BBS channel 9`
* This shows the SBBS address the wallet is listening on. This address can be copied and sent to Sender.
* If you want to create new SBBS address use the following command: `./beam-wallet --command=new_addr --listen -n 127.0.0.1:10000`

## Sending Beams

* To sending beams use the following command `./beam-wallet --command=send -n 127.0.0.1:10000 -r 77de6bd3de40bc58ab7e4fb68d5e0596fd1e72f3c4fb3eb3d106082d89264909 -a 11.3 -f 0.2`
* The send-related command line parameters of the wallet:
    * `-r` <SBBS address of the receiver node> 
    * `-a` <amount of beams to send>
    * `-f` <transaction fee>


## The Full list of wallet command line options

### General options:
* `-h [ --help ]`                         list of all options
* `-p [ --port ] arg (=10000)`            port to start the server on
* `--wallet_seed arg`                     secret key generation seed
* `--wallet_phrases arg                   phrases to generate secret key according to BIP-39. <wallet_seed>
 option will be ignored
* `--log_level arg`                       log level [info|debug|verbose]
* `--file_log_level arg`                  file log level [info|debug|verbose]
* `-v [ --version ]`                      return project version
* `--git_commit_hash`                     return commit hash

### Wallet options:
* `--pass arg`                            password for the wallet
* `-a [ --amount ] arg`                   amount to send (in Beams, 1 Beam = 1000000 chattle)
* `-f [ --fee ] arg (=0)`                 fee (in Beams, 1 Beam = 1000000 chattle)
* `-r [ --receiver_addr ] arg`            address of receiver
* `-n [ --node_addr ] arg`                address of node
* `--treasury_path arg (=treasury.mw)`    Block to create/append treasury to
* `--wallet_path arg (=wallet.db)`        path to wallet file
* `--bbs_keystore_path arg (=bbs_keys.db)` path to file with bbs keys
* `--tx_history`                          print transactions' history in info command
* `--tr_Count arg (=30)`                  treasury UTXO count
* `--tr_HeightStep arg (=1440)`           treasury UTXO height lock step
* `--tr_BeamsPerUtxo arg (=10)`           treasury value of each UTXO (in Beams)
* `--command arg`                         command to execute `[send|receive|listen|init|info|treasury]`

### Rules configuration:
* `--CoinbaseEmission arg (=80000000)`    coinbase emission in a single block
* `--MaturityCoinbase arg (=60)`          num of blocks before coinbase UTXO can be spent
* `--MaturityStd arg (=0)`                num of blocks before non-coinbase UTXO can be spent
* `--MaxBodySize arg (=1048576)`          Max block body size [bytes]
* `--DesiredRate_s arg (=60)`             Desired rate of generated blocks [seconds]
* `--DifficultyReviewCycle arg (=1440)`   num of blocks after which the mining difficulty can be adjusted
* `--MaxDifficultyChange arg (=2)`        Max difficulty change after each cycle (each step is roughly x2 complexity)
* `--TimestampAheadThreshold_s arg (=7200)` Block timestamp tolerance [seconds]
* `--WindowForMedian arg (=25)`           How many blocks are considered in calculating the timestamp median
* `--AllowPublicUtxos arg (=0)`           set to allow regular (non-coinbase) UTXO to have non-confidential signature
* `--FakePoW arg (=0)`                    Don't verify PoW. Mining is simulated by the timer. For tests only

## List of testnet nodes

The testnet nodes can be used in the following scenarios:
* When specifying peers for the beam-node
* Where a wallet connects to the specific node

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