# Transaction creation protocol

The process of creating transactions in Beam (and other MimbleWimble currencies) is interactive. 
In order to create new Beam transaction, wallets have to communicate with each other. During this negotiation they may exchange with a wide range of parameters which should allow to create a transaction they need. As a result, the protocol between wallets should be extendable.


## What is a transaction in Beam?
Any transaction should contain:
* List of input UTXOs (Inputs), they have to present in blockchain
* List of newly created UTXOs (Outputs) and rangeproofs for each output
* Explicit excess (offset)
* Kernel

### Kernel consists (at least):
* Blinded excess 
* Transaction fee
* Minimal and Maximal height values. These values allow to control the time while transaction is valid. Node will not accept transaction if its height is lower than minimal height and greater than maximum height
* Signature. This is a Schorr’s multi signature which signs all kernel’s values listed above

### Simple transaction flow.
Let’s we **Sender** wants to make a payment to **Receiver**.
* **Sender** and **Rreceiver** have to agree about `amount` and `fee`.
* **Sender** selects `inputs` which allow to pay `amount + fee`, if sum of 'inputs' is greater, **Sender** creates output for the change. **Sender** creates overall _blinding excess_ value `blindingExcess_S` and `offset_S`
* **Receiver** creates output for given `amount` and calculates _blinding excess_ `blindingExcess_R` and `offset_R`
* Both sides should generate _nonces_ `nonce_S` and `nonce_R` respectively.
* Both should pass each other public forms of excesses:
  * `publicNonce_S = nonce_S*G` and `publicNonce_R = nonce_R*G` – public nonces
  *  `publicExcess_S = blindingExcess_S*G` and `publicExcess_R = blindingExcess_R*G` – public blinding excessed
* Both have to calculate 
  * total blinding excess: `X = publicExcess_S + publicExcess_R`
  * total public nonce: `K = publicNonce_S + publicNonce_R`
* Both have to calculate Schorr’s signature challenge:
   * `e = H(K|M)`, where M is a signed message, it calculates from kernel and it includes X, fee, min height and max height
* Both calculate and pass to each other partial signatures:
  * S: `partialSignature_S = publicNonce_S + e*publicExcess_S`
  * R: `partialSignature_R = publicNonce_R + e*publicExcess_R`
* Final signature is calculated: `signature = partialSignature_S + partialSignature_R`

[[/images/SimpleTransactionFlow.png]]

## Wallet-To-Wallet protocol
The protocol itself consists of only one message. It should allow to implement all needed scenarios and transaction types. Also, this message can be encapsulated and passed to other part by using different means: as a direct message send over p2p connection or indirect with secure bulletin board system (SBBS) and others.

## SetTxParameter
Transfers a pack of transaction’s parameters from one wallet to another. This message may initiate a new transaction. 

-	`WalletID m_From` – the address which wallet wants to use for responses, used when sending messages over **SBBS**. `WalletID` is packed 8 bytes of BBS channel and 32 bytes of wallet’s public key.
-	`TxID m_TxID` – unique 16 byte transaction’s identifier. Generates by transaction initiator.
-	`TxType m_Type` – transaction’s type like `Simple, AtomicSwap` etc. This field is used to create new transaction object, when this message is the first in a line, or for verification purposes.
-	`std::vector<std::pair<TxParameterID, ByteBuffer>> m_Parameters` – vector of pairs of transaction’s parameters. Each parameter is a pair of ID from range [0...255], and value represented as a raw bytes buffer. ID values are separated in two parts: private and public (ids below `PrivateFirstParam == 128` are private). Public parameters come from outside and they are not allowed to be overridden. Private parameters do not have limitations.

## Example: Simple transaction

Simple transaction is a payment with change and fee from wallet A to wallet B. 

### Wallet A sends invitation.

```javascript
SetTxParameter
{
    m_From: XXXXXX // chosen wallet’s ID to receive response.
    m_TxID: 651798 //fills with newly generated random identifier.
    m_Type: TxType::Simple,
    [
        {TxParameterID::Amount, amount},
        {TxParameterID::Fee, fee},
        {TxParameterID::MinHeight, minHeight}, 
        {TxParameterID::MaxHeight, maxHeight}, 
        {TxParameterID::IsSender, false}, // flag to say recipient that it is not a sender of coins.
        {TxParameterID::PeerInputs, inputs}, 
        {TxParameterID::PeerOutputs, outputs},
        {TxParameterID::PeerPublicExcess, publicExcess}, 
        {TxParameterID::PeerPublicNonce, publicNonce},
        {TxParameterID::PeerOffset, offset}
    ]
}
```
* `minHeight `- if height of chain is less than this value, then created transaction will not be taken into account.
* `maxHeight` - if height of block chain is greater than this value then node will reject created transaction.
* `inputs` - vector of inputs (commitments) chosen by sender.
* `outputs` - vector of change outputs, created by sender.
* `publicExcess` - public form of sender’s excess calculated from blinding factors of inputs and change output.
* `publicNonce` - sender generates secret nonce, this is its public value.
* `offset` -  offset value, randomly taken part of change output blinding factor.



### Wallet B confirms invitation.
It creates output for received amount, generates nonce to sign transaction.
```javascript
SetTxParameter
{
    m_From: YYYYYY  // chosen wallet’s ID to receive response.
    m_TxID: 651798, // the same ID as sender.
    m_Type: TxType::Simple,

    [
        {TxParameterID::PeerPublicExcess, peerPublicExcess},
        {TxParameterID::PeerSignature, receiversPartialSignature},
        {TxParameterID::PeerPublicNonce, publicNonce}
    ]
}
```
* `peerPublicExcess` - receiver’s public excess, calculated from output’s blinding factors.
* `receiversPartialSignature` - receiver’s part of Schnorr multi signature.
* `publicNonce` - public form of nonce for signature.


### Wallet A confirms transaction. 
If receiver’s signature is valid, it calculates its part of signature
```javascript
SetTxParameter 
{
    m_From: XXXXXX // chosen wallet’s ID to receive response.
    m_TxID: 651798, 
    m_Type: TxType::Simple,
    [
        {TxParameterID::PeerSignature, sendersPartialSignature}
    ]
}
```
`sendersPartialSignature` - sender's signature calculated

Now wallet B has all data to create transaction and send it to node.

### Cancellation or error

If any of participants wishes to interrupt this process for any reason then it sends
```javascript
SetTxParameter 
{
    m_From: ZZZZZZ // chosen wallet’s ID to receive response
    m_TxID: 651798, 
    m_Type: TxType::Simple,
    [
        {TxParameterID::FailureReason, reason}
    ]
}
```
`reason` - 32 bit code of failure reason
