First let's define terminology.

* System state - is a _valid_ state of the system, which is fully defined by all the existing data (more about this later)
* Block - is an information used to perform a _transition_ from one system state to another.

To avoid ambiguity we refrain from saying "block hash" or "block height", because, strictly speaking, those are properties of the System state. OTOH blocks are just transactions, which may be combined.

Each state has a header, and there are several structures related to it.

### SystemState::ID

Consists of:
* `Merkle::Hash m_Hash`
* `Height m_Height`

Used to denote an existing known state. (Hash would be enough, but we decided to include the height as well)

### SystemState::Full

A full header bound to the system state. Consists of:
* `Height m_Height`
   * Our convention: `m_Height = 0` - is for initial system state, no data and nothing is interpreted yet. The next state, which is achieved after interpreting the genesis block, has `m_Height = 1`.
* `Merkle::Hash m_Prev`
   * explicit reference to prev
* `Difficulty::Raw m_ChainWork`
   * Cumulative chainwork (sum of difficulties) <u>including</u> the difficulty of this state.
* `Merkle::Hash m_Definition`
   * The Hash of the full system definition in this state (more about this later)
* `Timestamp m_TimeStamp`
* `PoW m_PoW`
   * Proof-of-Work. An equihash solution.
   * Contains the `Difficulty` of this specific state.

The _System State Hash_ is calculated from all the header parameters, <u>including PoW</u>. Not to be confused with `m_Definition`, it's different.

Note that when many such headers should be presented for consecutive states - there are 3 redundant elements:
* `m_Prev` - obviously equals to the Hash of the previous state
* `m_Height` - just increased by 1
* `m_ChainWork` - can be calculated from previous header, after adding the difficulty of this one.

So for those cases there are appropriate structures defined: `SystemState::Sequence::Prefix` and `SystemState::Sequence::Element`. And the `SystemState::Full` just inherits them both.

### PoW

BEAM uses equihash mining algorithm. It includes the following:
* `Difficulty m_Difficulty`
* `m_Nonce` - arbitrary 64-bit values
* `m_Indices` - the solution, array of sorted indexes.

The input for the solver/verifier is constructed from all the Header fields, except the PoW solution itself. But it does include the `m_Nonce` and the `m_Difficulty`. So that the difficulty must be selected before the mining, and can't be adjusted a-posteriori, if the solution eventually could reach higher difficulty (reached a smaller target).

**Note**: The whole PoW parameters, including the solution, are accounted for in calculating the System State Hash. This is an intentional design decision, which ensures it's not possible to construct the chain of the system state headers without actually mining them, and then be able to mine only some specific states on-demand. This assumption is essential for _FlyClient_ protocol.

# System Definition Hash

One of the most important parameters. The complete system state is constructed from the following data:
* All the existing non-spent UTXOs, stored with the relevant parameters in an `UtxoTree`, which is a variant of the `RadixTree`.
* All the existing Kernels, stored in the `RadixTree`.
* MMR of all the inherited system states.

The System Definition Hash is defined as:

`System-Definition-Hash = Hash [ InheritedStates.Root | Hash (UTXOs.Root | Kernels.Root) ]`

After interpreting the appropriate block, the Full Node evaluates the actual System-Definition-Hash, and compares it with value in the State Header.

Note also that it's actually a root hash of the Merkle tree, whose elements are another Merkle trees. Using this property it's possible to generate and verify Merkle proofs for:
* Existing UTXO in the current system state
* Existing Kernel
* Inherited System State

All the Verifier needs is the proof, and the System Definition Hash. In addition to verifying the proof, the Verifier ensures the proof suffix is in accordance to the known part of the Merkle tree structure. Means:
* For UTXOs and Kernels the Verifier knows the hashing direction of the last 2 elements.
* For inherited System State the Verifier knows the whole Merkle path, hence all the hashing directions are deduced automatically.


