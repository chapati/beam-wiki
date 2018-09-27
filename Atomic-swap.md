Atomic swap is performed between Beam and other cryptocurrency. Denote it BTC, but actually it can denote other network that supports the needed functionality.

The swap is performed in a single transaction, which effectively transfers the ownership of the Beam UTXO in exchange for some secret, which is needed to claim the being-exchanged coin on the BTC network.

# High-level design

There are two parties:
* **A**lice. Owns the Beam, interested to trade it for the BTC
* **B**ob. Owns the BTC, interested to trade it for the Beam.

There are 3 major phases of the atomic swap
1. Prerequesites
   * The being-exchanged UTXOs are locked on both networks for a specific time period (in terms of blocks).
   * Parties monitor both networks to ensure the source UTXOs are indeed locked.
2. Exchange
   * Parties collaborate to create a transaction that transfers the locked Beam to **B** in exchange for the secret.
   * **B** substitutes the secret to the transaction and finally broadcasts it to the network.
   * Once transaction is visible - **A** learns the secret.
   * **A** creates a BTC transaction to claim the BTC UTXO, and broadcasts it to the BTC network.
3. Rollback. In case the swap didn't take place (for whatever reason)
   * Locked UTXOs on both network should remain intact until their lock timeout expires.
   * After the timeout expiration parties broadcast transactions that transfers the locked UTXOs back to them.

Note that **A** actually claims the BTC UTXO <u>after</u> sending the Beam UTXO. This means that the lock timeout of the BTC UTXO must be significantly bigger than that of the Beam UTXO, because **B** may broadcast the Beam transaction just before the timeout expires, the **A** should still have enough time to build and broadcast the BTC transaction.

# Technical design

The _unlock secret_ is the SHA-256 hash preimage (i.e. a 256-bit value which, after hashing, should be equal to a known 256-bit value). Supported on Beam, BTC, and, probably, many other networks.

There are no scripts in Beam blockchain. However the following is supported in the transaction kernel:
* Timelock - minimum blockchain height for the kernel to be valid
* HashPreimage

## UTXO lock on BTC network

**B** broadcasts a BTC transaction, which creates an UTXO which can be spent under the following conditions:
1. Timeout + secret key chosen by **B**
1. Hash preimage chosen by **B** + secret key chosen by **A**

## UTXO lock on Beam network

This part is somewhat more complex, since there are no scripts in Beam. **A** and **B** collaborate to create a _shared_ UTXO, whose blinding factor is shared between them.

1. **A** and **B** randomly choose their parts of the blinding factor for the shared UTXO
1. **A** and **B** first create the "rollback" transaction, which would transfer the shared UTXO back to **A** if the exchange didn't take place.
   * This transaction kernel is Time-locked, i.e. transaction can only be broadcasted starting from specific blockchain height.
   * **A** saves this transaction, but doesn't broadcast yet.
1. **A** and **B** create the transaction that spends **A**'s UTXO and creates the shared one.
   * The tricky part is creating the Bulletproof for the shared UTXO. Takes 3 iterations.
1. They broadcast this transaction to the Beam network, to create the shared UTXO.

## Exchange transaction

At this point both Beam and BTC UTXOs are locked, both parties get confirmations for this. In addition **A** knows the _Image_ of the locked BTC UTXO. Now they collaborate to build a Beam transaction that transfers the shared (locked) Beam UTXO to **B** in exchange for revealing the _Hash Preimage_, which, after hashing, equals to the known _Image_.

* **B** randomly chooses the blinding factor for the new UTXO
* **A** creates the Transaction Kernel, which is supposed to contain the _Hash Preimage_ (but doesn't contain yet), which corresponds to the knwo _Image_.
* **A** puts her part of the kernel multisig, which assumes the correct _Image_.
* Incomplete transaction is passed to **B**.
* **B** puts his part of the kernel multisig, which assumes the correct _Image_ (same as Alice).
* <u>**B** substitutes the correct _Hash Preimage_ to make the transaction kernel valid.</u>
   * This is where the atomic swap actually occurs!
* **B** broadcasts the transaction to the Beam network.

**A** monitors the Beam network. Once the exchange transaction kernel is visible:
* **A** realized that the exchange occurred.
* **A** learns the _Hash Preimage_.
* **A** creates and broadcasts the BTC transaction to claim her BTC UTXO.

