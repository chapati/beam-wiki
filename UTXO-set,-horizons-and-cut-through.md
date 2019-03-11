One of the MW benefits is the cut-through, i.e. ability to discard the spent TXOs, yet be able to prove the authenticity of the current state.

# Horizons

The term _horizon_ in this context denotes a relative distance (in terms of blocks) from the current blockchain tip, subtracting it from the current blockchain height we get the appropriate height that corresponds to this horizon. The blocks and TXOs created/spent below this height are subject to a special processing.

In Beam there are 3 horizons defined:

1. Max-rollback distance

    Fixed, part of the consensus parameters. For mainnet equals to 1440, which corresponds to 1 day for expected block creation rate.
1. Hi-Horizon
1. Lo-Horizon

The latter 2 horizons can be set to arbitrary values and differ in each node. The following criteria however must be satisfied:
 
* `Max-rollback-distance` <= `Hi-Horizon` <= `Lo-Horizon`

or in terms of heights:

* `Max-rollback-Height` >= `Hi-Height` >= `Lo-Height`

## Max-rollback distance

This horizon, as its name suggests, defines the maximum number of the recent blocks accepted by the node as the current consensus branch, that can potentially be reverted in order to switch to a competing branch. Or, speaking simply, this is how deep the reorg can be.

So that recent blocks are volatile (subject to potential reorgs), but blocks below this height can be considered stable. This defines the minimum number of the recent blocks that each node is obliged to keep (or be able to re-create).

## Hi-Horizon

Defines how long the spent TXO must be <u>fully</u> kept in the node after it was spent.

For TXOs that are spent below the current `Hi-Height` (which is the current blockchain height minus `Hi-Horizon`) the node will keep the TXO commitment, but not the signature (bulletproof). This is called _Reduced TXO_ (a.k.a. naked, diluted, skeleton).

Since the UTXO signature takes most of its size (~95%), getting rid of it has a dramatic impact on the storage size and the amount of information that should be negotiated when such a reduced TXO sent.

Obviously reduced TXO can't be trusted to be a valid TXO object (well-formed, with unknown positive value restricted to 64 bits). However they play an important role in the synchronization process (as we'll see later), and as long as the UTXO set (i.e. unspent TXOs only) doesn't contain reduced TXOs - there is no problem.

## Lo-Horizon

Defines how long the reduced spent TXO must be kept in the node after it was spent. TXOs that are spent below the current `Lo-Height` (which is the current blockchain height minus `Lo-Horizon`) will be completely erased in the node, without any further trace of existence.

# Synchronization

During standard synchronization process blocks are downloaded and verified one by one (as in most of the blockchains). However each node can decide to skip several intermediate steps, and "jump" to an arbitrary new height. The goal of the cut-through is to support such jumps with minimal amount of information transferred.

## Sparse blocks

In Beam this is achieved by using _sparse blocks_, that contain only parts of the original blocks that are essential to achieve the requested final state. So that during synchronization the node still downloads individual blocks, but they are reduced to the minimum, their verification is delayed until they are all downloaded, and only the final state is fully verified.

When requesting the sparse block the following information must be specified:
* Block identifier (`Height`, `Hash`)
* `H0` - Current height of the requesting node (i.e. start height of the jump)
* `Lo-Height` and `Hi-Height` that the node wants to achieve <u>**after**</u> the jump

The sparse block is generated on-the-fly by the target node. It includes all the original kernels (which are, obviously, not a subject to cut-through), as well as inputs and outputs after the following filtration:
1. Remove all the inputs and outputs for TXOs that were created above `H0` and spent below or equal `Lo-Height`
1. Reduce outputs (i.e. remove the bulletproof) that are spent below or equal `Hi-Height` (unless already removed by (1))

This comes down to the following processing for every input and output
* Inputs
   * if `SpendHeight` > `Lo-Height` then include
   * if `CreateHeight` <= `H0` then include
   * if Otherwise - exclude (skip)
* Outputs
   * if `SpendHeight` > `Hi-Height` then include full
   * if `SpendHeight` > `Lo-Height` then include reduced
   * if Otherwise - exclude (skip)

## Pros and Cons of this scheme

The main advantage of the described scheme is its <u>versatility</u>. Unlike older scheme which supported cut-through only from the genesis, this scheme supports arbitrary height jumps.
A typical use-case is a node/wallet that was offline for several days/weeks - will be able to download only the minimum info to perform the transition to the most recent state.

Another advantage is that in order to support this the target node doesn't need to "work hard", generate a lot of data on-the-fly or prepare in advance (consuming extra storage), which can be exploited by the attacker. With proper data structures generation of sparse blocks can be nearly as fast as retrieving the original blocks.

The price of this versatility is the need to keep the reduced TXOs for reasonably long duration. This is their role. As can be seen, in order to be able to create the sparse block, in particular include all the needed inputs, the target node must keep the information about spent TXOs: their commitment, create and spend heights.

So that the `Lo-Horizon` parameter affects the ability of the node to generate the cut-through data for other nodes. A "selfish" node may set `Lo-Horizon` equal to `Hi-Horizon`, means it won't keep reduced TXOs at all. Such a node will only be able to provide cut-through info for others from the genesis. But, as, we said, the reduced TXOs are dramatically smaller than full TXOs, so that keeping them for reasonable duration (months) should not affect the storage size significantly.

## Cut-through verification

After all the sparse blocks are downloaded, the following is verified:
* For each block:
    * Kernels are valid. `Heightlock` is in agreement with the block height, Schnorr's signature is valid.
    * Kernel commitment (MMR root of the kernels of this block) corresponds to the header. This is very important, means all the original transactions are included.
    * All inputs reference existing UTXOs in the current state (i.e. TXOs that are unspent yet).
    * All non-reduced outputs have valid bulletproofs.
    * All inputs and outputs are interpreted, and the UTXO set undergoes appropriate transformation.
* For each block above or equal `Lo-Height` (in addition to the mentioned):
    * UTXO set commitment corresponds to the header.
        * This verification is possible only from `Lo-Height` and above, since below this height there may be some TXOs, info about which was totally omitted.
        * This verification is not essential to prove the validity of the final state, however it should harden DOS attacks, where the attacker may provide fake inputs and appropriate outputs in later blocks, making this node generate incorrect sparse blocks for others later.
* In addition, after verifying all individual blocks:
    * Overall arithmetics. The sum of all outputs minus inputs corresponds to the overall blockchain subsidy for the specified height range.
    * <u>No reduced UTXOs</u>. The current UTXO set (unspent TXOs) only contains well-formed TXOs with bulletproofs.

# Node configuration and synchronization logic

As we said, the horizons may be set to arbitrary values (as long as `Max-rollback-distance` <= `Hi-Horizon` <= `Lo-Horizon`). But practically we use only the following configurations:

* Archiving node
    * `Hi-Horizon` is infinite
    * `Lo-Horizon` is infinite
    * Always performs a comprehensive synchronization, never deletes history, and can generate any sparse block on request.
* Standard node
    * `Hi-Horizon` = `Max-rollback-distance` = 1440
    * `Lo-Horizon` = 1440 * 180
    * Keeps the most recent history, plus a half-year backlog in terms of reduced TXOs (very modest size). Supports sparse blocks to other standard nodes, unless they were offline for more than half a year.

## Cut-through mode activation

Cut-through mode is activated automatically when the following criterias are met:
* Node is not already in the cut-through mode
* There is a **proven** state with height which is at least current node height + `Hi-Horizon` * 1.5
    *  _Proven_ means all the headers starting from this one down to the genesis are already downloaded and verified

Once this happens - node enters the cut-through mode. 
* `Target-Hi-Height` is set to `ProvenTip.Height` - `Hi-Horizon`
* `Target-Lo-Height` is set to `ProvenTip.Height` - `Lo-Horizon`

Note that once selected the `Target-Lo-Height` can **not** be changed till the end of the synchronization. The `Target-Hi-Height` however can, and will be increased each time the node will see a higher proven tip.

Once all the sparse blocks are downloaded - they are verified (according to the scheme described above). If the verification passes - the node switches to the standard mode of operation. Otherwise all the downloaded sparse blocks are erased, and the process restarts.
