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
   * Our convention: counting starts from 1. This is the height of the first non-zero state
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

The _System State Hash_ is calculated from all the header parameters, including PoW. Not to be confused with `m_Definition`, it's different.

Note that when many such headers should be presented for consecutive states - there are 3 redundant elements:
* `m_Prev` - obviously equals to the Hash of the previous state
* `m_Height` - just increased by 1
* `m_ChainWork` - can be calculated from previous header, after adding the difficulty of this one.

So for those cases there are appropriate structures defined: `SystemState::Sequence::Prefix` and `SystemState::Sequence::Element`. And the `SystemState::Full` just inherits them both.

# System Definition Hash

One of the most important parameters.

... WIP ...