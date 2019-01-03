This page contains general information. For Step By Step guides on how to start Mining Beam please refer to:

https://beam-docs.readthedocs.io/en/latest/rtd_pages/user_mining_beam.html


Alike most cryptocurrencies, Beam relies on miners to add transactions to the blockchain. While all nodes in the Beam network confirm the validity of transactions, Beam counts on miners to take on the massive heavy lifting to guard the network.

Beam is a Mimblewimble implementation. We use classic Proof-of-Work (PoW) consensus.

We welcome everyone to join our mining community to support the network and earn Beam coins. 

# Mining Algorithm

To secure the network, Beam uses the [Equihash](https://www.cryptolux.org/index.php/Equihash) proof-of-work mining algorithm). Miners compete against each other using their computing power produce a new block on the chain. The first miner that gets to complete the precise computation for each block is granted with a network standard block reward and any fees for transactions added to that block.

At Mainnet launch, we will use the following Equihash parameters: **n=150**, **k=5**. In addition, we will introduce a small change to the datapath to further reduce the chance of zero-day ASICs.

Note: in Testnet 3 we are still using **n=144**, **k=5** 

The minimal memory requirement for the GPU will be 4 GB. The most up-to-date list of supported GPUs will be available [here](https://github.com/BeamMW/beam/wiki/Supported-GPU-cards-for-mining-(NVIDIA)).

# Block Size and Time
A Beam block will be generated approximately every minute and contain about 1000 transactions. Block size will be roughly 1MB.

# Miner Rewards
## Block Rewards
During the first year of Beam existence, miner reward will be 80 coins per block. In years 2-5 the reward will be 40 coins per block. In year 6 the reward will be to 25 coins, and then halving will occur every 4 years until year 129. After year 133, Beam emission will stop.

## Treasury
In the first five years of existence, additional coins will be issued to Beam Treasury with each newly mined Beam block.

In the first year, the Treasury will receive additional 20 Beams per  block, and in the years 2-5 the Treasury will receive 10 coins per block.

The Treasury will be used to repay Beam investors, Incentivize the Core Team and to support the Beam Foundation (largest single beneficiary of the Treasury). 

The distribution of the Treasury Coins is performed on a quarterly basis in the following proportion:
* Investors: 40%
* Core Team: 40%
* Beam Foundation: 20% (Biggest single beneficiary)

## ASIC Resistance
To ensure better decentralization, Beam plans to stay ASIC resistance in the first 12-18 months. To achieve this, we plan to perform one or two hard forks – first after approximately 6 months of existence and another one after approximately 12 months. Each hard fork will change the mining algorithm. The exact modifications will be revealed several weeks before the actual hard fork. 

## How to Mine Beam
### Mining with Desktop Wallet
Our official [Desktop Wallet](https://www.beam.mw/downloads) supports both CPU and GPU mining.

To mine Beam using the Wallet, go to the Settings screen, select “Run Local Node” and set the number of Mining Threads to a value that is greater than zero. Note: the more threads you dedicate to mining, the more strain on your CPU. If you have a supported GPU, turn the “Use GPU” switch to On. 
If you have a [supported GPU](https://github.com/BeamMW/beam/wiki/Supported-GPU-cards-for-mining-(NVIDIA)), you can also choose “Use GPU” in the Wallet settings. Thread count is not relevant for GPU. Make sure your GPU has the latest drivers installed.

Please bear in mind that since GPUs are much more capable of parallel computations than CPUs, mining with GPUs is much more efficient.

### Mining with Standalone Node 
#### Using Built-in Miner
For advanced miners, we recommend downloading a standalone Beam node and configuring it to mine either with CPU or with GPU. You can download the Node software [here](https://www.beam.mw/downloads).
Instructions for running the node are available [here](https://github.com/beam-mw/beam/wiki/Instructions-for-Command-Line-Node). If you have a supported GPU, make sure to set the `miner_type` parameter to `gpu` in `beam-node.cfg` file.

#### Using External Miner
For more complex configurations, one or more external miners can be connected to a Beam Node instance.
The external miner software is available for download [here](https://www.beam.mw/downloads), and the detailed instructions for running it are (here)[https://github.com/beam-mw/beam/wiki/Instructions-for-Command-Line-GPU-Miner].

# How can you help?
* Want to help developing Beam mining infrastructure? Have GPU development skills or experience with mining pools? Write to us at mining@beam.mw. Fair compensation will be offered to qualified developers.
* Own a mining farm and want to mine Beam at launch? Join Beam Mining community on Telegram or contact us at mining@beam.mw.





