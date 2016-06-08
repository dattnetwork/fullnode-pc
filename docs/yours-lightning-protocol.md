Yours Lightning Protocol
========================

Notes
-----
- Payments can go both ways on a channel, so the agent who ultimately receives
  a payment is the one who generates the secret.
- "Dave" is the ultimate recipient on the close end of the payment channel, and
  "Carol" is the ultimate recipient on the far end of the channel.

Definitions
-----------
- Bob (B) opens a payment channel with Carol (C). Alice (A) wishes to make a
  payment to Dave (D). The channel from Bob to Carol is used for the payment
  from Alice to Dave (and other channels may be involved as well). Alice and
  Bob may be the same agent. Carol and Dave may be the same agent.
- The transaction fee is F.
- The channel is funded with N satoshis.
- The payment is M satoshis, and M < N.
- The change is R = N - M - F, and it must be that R >= 0.
- The transaction fee for a commitment tx is F satoshis.
- An HTLC secret is 32 bytes long and its hash is 20 bytes long.
- An RHTLC secret is 32 bytes long and its hash is 20 bytes long.

Message Data Structures
-----------------------
All message types are JSON objects with these properties:
- cmd: A string, specifying the name of the command to be executed by the
  remote agent.
- args: A JSON object (optionally an array) containing the properties the
  remote agent needs to execute the method.
- chanId: A 32 character hex string identifying the channel. The chanId is set
  by the channel initiator and must be the same for every message for that
  channel.

### MsgOpen
- Command: 'open'
- Arguments:
  - amount: A big number specifying the amount of satoshis the channel will be
    funded with.
  - pubKey: The channel funder's public key (in hex format) to be used in the
    funding multisig transaction.
- Explanation: This message is the first message sent on a channel. When Bob
  opens a channel with Carol, Bob sends this message first.

### MsgOpenRes
- Command: 'open-res'
- Arguments:
  - pubKey: The public key (in hex format) of the channel recipient.
- Explanation: If a channel recipient (Carol) has received an 'open' message
  and agrees to open the channel, they respond with an 'open-res' message. This
  message needs to contain the public key of the channel recipient for use in
  the funding multisig transaction.

### MsgError
- Command: 'error'
- Arguments:
  - error: An error string that explains the nature of the error.
- Explanation: When a fatal error occurs, an error message may be sent. Both
  parties should close the channel when an error message is either sent or
  received.