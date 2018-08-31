# Core data types used in BEAM:
* `Height` - 64-bit unsigned integer
* `HeightRange` - a pair of `Height`
* `Timestamp` - 64-bit unsigned integer
* `Amount` - used to denote the value of a single UTXO. 64-bit unsigned integer
* `AmountBig` - used to denote the value of an arbitrary number of UTXOs. Consists of 2 64-bit unsigned integer (i.e. equivalent to 128-bit integer)
* `Difficulty` - 32-bit encoding of a floating-point number. 8 bits for mantissa, 24 bits for order.
* `Difficulty::Raw` - 256-bit unsigned integer. Represents an "unpacked" `Difficulty` on a linear scale. Used to represent the chainwork (sum of difficulties).


# Input UTXO

Consists of the following:
* `ECC::Point m_Commitment`
   * A commitment to the UTXO (which is supposed to exist in the system)
* `Height m_Maturity`
   * Optional field to specify the maturity of the UTXO being-spent.
   * Used only in _Macroblocks_ (compressed history block), more about this later. Illegal to use in transactions.

# Output UTXO

Consists of the following:
* `ECC::Point m_Commitment`
   * A commitment to the UTXO (which is supposed to exist in the system)
* `Height m_Maturity`
   * Optional field to specify the maturity of the created UTXO.
   * Used only in _Macroblocks_ (compressed history block), more about this later. Illegal to use in transactions.
* `bool m_Coinbase`
   * Must be set iff it's a coinbase UTXO.
* `Height m_Incubation`
   * Optional field. If specified - the number of extra blocks (in addition to standard system rules) until the UTXO becomes mature.
* UTXO Signature. Must be one of the following:
   * `ECC::RangeProof::Confidential` - a confidential signature (Bulletproof)
   * `ECC::RangeProof::Public` - a public signature, with the Amount visible.

### UTXO Signature

* Coinbase UTXO must have a public signature (the Amount explicitly specified).
* Non-coinbase UTXO - depends on the system rules. In the current configuration all non-coinbase UTXOs are enforced to have a confidential signature, but may also be configured to allow public signatures.

The confidential signature is implemented in terms of a Bulletproof. The public signature consists of the following:
* `Amount m_Value` - the explicit value
* `ECC::Signature m_Signature` - a Schnorr's signature for the UTXO blinding factor.

**Note**: the `m_Incubation` field is accounted in the UTXO signature, to prevent tampering.

# Transaction Kernel

Consists of the following:
* `ECC::Point m_Excess`
   * The transaction excess (public)
* `ECC::Signature m_Signature`
   * The Schnorr's signature for the whole kernel body, signed with the transaction excess (secret)
* `Amount m_Fee`
   * Transaction fee
* `uint64_t m_Multiplier`
   * Optional field, used in a scheme where a kernel may be consumed (more about this later)
* `HeightRange m_Height`
   * Optional: the transaction timelock parameters (min/max height for being valid in the block)
* `uintBig m_HashLock`
   * Optional: a hashlock. An arbitrary 256-bit value which is hashed, and then accounted in the kernel signature
* `m_vNested`
   * Optional: an array of _nested_ kernels. More about this later.

## Kernel Signature

The Hash of the kernel is evaluated, and then it's signed by the Schnorr's signature. The Hash evaluation formula is:

>     M = m_Fee | m_Height;
>     if Hashlock specified
>     M = M | true | Hash(Hashlock.Preimage);
>     else
>       M = M | false;
> 
>     For each Nested Kernel
>        M = M | true | (Nested Kernel).ID;
> 
>     M = M | false
> 
>     KernelHash = Hash(M)

The formula accounts for all the members except except the signature and the excess (which is a signature public key). It's also unambiguous (i.e. explicit separation for optional input fields).

## Kernel ID

According to the system rules every kernel has a unique ID (duplicate IDs are banned). It's calculated by the formula:
>     KernelID = Hash( KernelHash | m_Excess | m_Multiplier );
>     if (KernelID == 0)
>        KernelID = 1;
In addition to the kernel parameters (encapsulated within `KernelHash`) it also accounts for the kernel excess.
In addition there are forbidden values (reserved for internal system use). Currently it's only 0.

# Transaction
Consists of the following:
 * Input UTXOs
 * Output UTXOs
 * Input Kernels
    * This is the BEAM extension - kernels may also be consumed! More about this later.
 * Output Kernels
 * `ECC::Scalar m_Offset`

## Transaction validation

The following is validated:
* All the transaction elements (UTXOs, Kernels) must be specified in a well-defined order (there's a comparison function for each transaction element type).
* Each element must have a valid signature
* Timelocks of all the kernels must be valid for the current height

Next, we define the <b>&Sigma;</b> as a sum of all outputs minus all inputs:

<b>&Sigma;</b> = &Sigma;(UTXO-Out.Commit) - &Sigma;(UTXO-In.Commit) + &Sigma;(Kernel-Out.Excess) - &Sigma;(Kernel-In.Excess) + m_Offset &middot; G

In case of transaction validation there are transaction fees that are considered lost, i.e. they are implicit outputs. So, the validation formula is:

<b>&Sigma;</b> + &Sigma;(Fees)&middot;H = 0

# Block body
Contains all that is included in a transaction. In addition:
* `AmountBig m_Subsidy`
  * explicit Amount created by this block (Coinbase and, possibly, other types of subsidy)
* `bool m_SubsidyClosing`
  * If set - means from the next block only coinbase subsidy should be allowed

## Block body validation (context-free)

The block body is validated similar to a transaction, keeping in mind however that this may be a _Macroblock_, i.e. a merged block for a specific height range (applicable to the Timelock validation of kernels).

Then <b>&Sigma;</b> is calculated. But in contrast to transaction the Fees should not be added, because they must have already been consumed by explicit output UTXO(s) (injected by the miner). Instead the explicit Subsidy must be accounted as an input. Hence the formula is:

<b>&Sigma;</b> - m_Subsidy&middot;H = 0

In addition the Verifier must verify that the Coinbase UTXOs are properly tagged, and unspent unless the coinbase maturity is reached.
For a single block verification the sum of all Coinbase UTXO amount must be equal to the single block Coinbase emission as defined by the system rules.
For _Macroblock_ verification the Verifier should take into account that some Coinbase UTXOs may have already been spent.
