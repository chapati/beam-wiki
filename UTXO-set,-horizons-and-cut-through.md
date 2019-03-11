One of the MW benefits is the cut-through, i.e. ability to discard the spent TXOs, yet be able to prove the authenticity of the current state.

# Horizons

The term _horizon_ in this context denotes a relative distance (in terms of blocks) from the current blockchain tip, subtracting it from the current blockchain height we get the appropriate height that corresponds to this horizon. The blocks and TXOs created/spent below this height are subject to a special processing.

In Beam there are 3 horizons defined:

1. Max-rollback distance
   Fixed, part of the consensus parameters. For mainnet equals to 1440, which corresponds to 1 day for expected block creation rate.
1. Hi-Horizon
1. Lo-Horizon

The latter 2 horizons can be set to arbitrary values and differ in each node. The following criteria however must be satisfied:
 
<code>(Max-rollback-distance) <= (Hi-Horizon) <= (Lo-Horizon)</code>

or in terms of heights:

<code>(Max-rollback-Height) >= (Hi-Height) >= (Lo-Height)</code>

## Max-rollback distance

This horizons, as its name suggests, defines the maximum number of the recent blocks accepted by the node as the current consensus branch, that can potentially be reverted in order to switch to a competing branch. Or, speaking simply, this is how deep the reorg can be.

So that recent blocks are volatile (subject to potential reorgs), but blocks below this height can be considered stable. This defines the minimum number of the recent blocks that each node is obliged to keep (or be able to re-create).

## Hi-Horizon

Defines how long the spent TXO must be <u>fully</u> kept in the node after it was spent.

For TXOs that are spent below the current _Hi-Height_ (which is the current blockchain height minus _Hi-Horizon_) the node will keep the TXO commitment, but not the signature (bulletproof). This is called _Reduced TXO_ (a.k.a. naked, diluted, skeleton).

Since the UTXO signature takes most of its size (~95%), getting rid of it has a dramatic impact on the storage size and the amount of information that should be negotiated when such a reduced TXO sent. However, obviously, reduced TXO can't be trusted to be a valid UTXO object (well-formed, with positive value restricted to 64 bits).

