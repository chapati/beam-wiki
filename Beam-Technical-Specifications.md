BEAM implements the MW protocol (with some extensions), which is based on elliptic curve cryptography (ECC).

* [Cryptographic primitives](https://github.com/beam-mw/beam/wiki/Cryptographic-primitives)
* [Merkle trees](https://github.com/beam-mw/beam/wiki/Merkle-trees)

MimbleWimble objects in details
The following are the objects:

Input UTXO reference
Output UTXO
Transaction kernel
Input
Represents a reference to an existing UTXO. Consists just of a commitment (a curve point). Nothing to discuss.

Output
Represents a new UTXO that should be created by the specific transaction. Consists of the following:

Point: Commitment
Boolean: Flag for Coinbase/regular
uint64: Incubation period
Signature: either public or confidential
The "Incubation period" specifies the extra maturity (in addition to the system rules) for the UTXO until it becomes mature, i.e. may be spent in a transaction. It's exposed to the Oracle, which is used for the signature, to prevent tampering. The signature attached to the UTXO proves that:

The encoded amount is indeed within the allowed range: 64 bits
The creator of the UTXO knows the opening of the commitment. Means - it's not a fake object.
Signature does not reveal the UTXO blinding factor
The signature can be either public or confidential, the appropriate must be attached according to the system rules. The rules are:

Coinbase UTXO must be signed with public signature, i.e. the encoded amount must be visible.
Non-coinbase UTXO must be signed by confidential signature. The public signature is disallowed by default, but may be enabled in system configuration. Subject to design decision.
Public UTXO signature
Consists of the following:

uint64: The encoded Value.
Schnorr's signature for the blinding.
The prover exposes the Value to the Oracle, and then proceeds according to Schnorr's protocol to prove the knowledge of the private key, which is the UTXO blinding factor.

The verifier subtracts the Value*H  from the commitment, this is the UTXO public key. Then exposes the Value to the Oracle, and proceeds according to Schnorr's protocol to verify the knowledge of the appropriate private key.

Confidential UTXO signature
Consists of 16 Points and 5 Scalars, making it 674 bytes long. More about this later

Kernel
The following are the fields

Mandatory
Point: Excess
Schnorr's signature
Optional
uint64: Fee
uint64: Multiplier (more about this later)
uint64 x 2: LockHeight
Hashlock
Contract
Nested (inner) kernels
In the simplest scenario contains just an excess point and a signature. The signature proves the following:

The preimage of the Excess (the scalar) is known to the kernel creator.
The kernel wasn't tampered with after it was signed.


Transaction and Block validation
Transactions and blocks have similar structure and validation rules

Transaction consists of the following:

List of input UTXOs
List of input kernels (this is our extension to the classical MW)
Lost of output UTXOs
List of output kernels
Scalar: Offset
Block only
uint128: Subsidy - total amount created by this block
Boolean: SubsidyClose flag.


When a transaction/block is received, the following two types of validation are performed:

Context-free validation.
Does not validate the existence of the referenced inputs
Validates all the signatures and the overall arithmetics (more about this later)
This is the most CPU-hungry part, but luckily it can be parallelized (more about this later)
State transition
Performs the state transition of the system wrt transaction/block, i.e. consuming the inputs and creating the outputs iff such a transition is valid according to the rules.
This involves in particular verification that all the referenced inputs exist and mature enough
The state transition is reversible, means it can be "played backward". This however requires saving some auxiliary information during the forward transition (more about this later).
Context-free validation
The following is checked:

All the output UTXOs and kernels are properly signed
All the objects of the same kind (within a list) are specified in the lexicographical order
Those kernels that contain LockHeight are verified vs current height.
Transactions only: Coinbase output UTXOs are not allowed.
The following is calculated:

Point: Sigma. Equals to the sum of all outputs minus all inputs. Defined as:
Sigma = Σ(Output UTXOs) - Σ(Input UTXOs) + Σ(Output Kernels.Excess) - Σ(Input Kernels.Excess) + Offset * G
Total Value of coinbase output UTXOs
Total Fee value.
Transaction-specific
Transactions must not have the coinbase output UTXOs, and all the fees are considered lost, i.e. they are implicit outputs that should be added to verify the overall validity. Hence the final validation criteria is:

Sigma + Σ(Fee) * H = 0

Block-specific
In blocks fees are already consumed (by the miner). To account for the block subsidy (mined coins) the validation criteria is:

Sigma - Subsidy * H = 0

In addition the verifier check that both the block subsidy conforms to the emission rules of the system, and that this amount is encoded within the Coinbase output UTXOs (means, the emission is not "hidden" in non-coinbase UTXOs).

Note that block in MW can be merged, so that the block being-verified may represent the state transition of multiple original blocks, with the appropriate height range. This must be accounted when checking the block subsidy and the values encoded with Coinbase UTXOs that are not spent yet. More about this later.

System state
