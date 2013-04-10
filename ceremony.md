Covert Cash Conversion Ceremony
===============================

The following procedure describes a ceremony for mixing bitcoins in such a way that the mix operator can neither:

1. trace the identities of the participants of a mix round, or
2. make off with the funds being mixed (only the fee, if they wish).

The protocol consists of three phases, plus an overture.

The mix operator should maintain a persistent identity. They should use this identity to certify each exchange specification they publish. Clients should be able to use this identity to verify communications from the mix operator.

When this description talks about communications, two caveats should be noted. Clients should make sure to communicate with the mix operator over a secure, anonymous channel, such as Tor. To maintain clients' confidence in the integrity of the process, the mix operator should promptly publish each transmission received.

Overture
--------

The mix operator publishes the specifications of an exchange round. This includes the following details:

* A string unique to this mix operator; an identifier for this instance of the mix ceremony: the `exchange-id`.
* A signing key to be used only for this exchange: the `exchange-key`.
* The number of bitcoins which each participant will mix in this exchange: the `denomination`.
* The minimum number of participants for this exchange to proceed: `min-participants`.
* The maximum number of participants for which this exchange will occur `max-participants`.
* The amount which must be paid to the mix operator for this exchange: the `fee`.
* A `timeline`, consisting of:
    * The time at which phase 1 will start: `phase-1`.
    * The time at which phase 1 will end and phase 2 will start: `phase-2`.
    * The time at which phase 2 will end and phase 3 will start: `phase-3`.
    * The time at which phase 3 will end and this exchange will be complete: `complete`.


Clients who have read the exchange specification and want to participate produce the following things:

* A bitcoin address which owns at least the `denomination`: their `input-address`.
* A bitcoin address at which they wish their payout deposited: their `output-address`.

The client blinds their `output-address` with a secret known only to them. This will be used in phase one.

If a client's `input-address` contains more bitcoin than the `denomination`, it is assumed that the excess is a contribution to a bitcoin transaction fee for this exchange.


Phase One
---------

1. Each interested client contacts the mix operator, specifies which `exchange-id` interests them, then transmits their `input-address` and blinded `output-address`.
2. If the exchange has not yet reached `max-participants`, it replies with an address at which they would like the fee deposited.
3. Once the mix operator is in receipt of the fee, they sign the blinded `output-address` with the `exchange-key`, and return this to the client.


Phase Two
---------

0. If `min-participants` has not been reached, the mix operator may return the fee, and this exchange is over. The mix operator may perform other analysis of the inputs at this time and decide not to proceed because this exchange would not provide sufficient anonymity.
1. Each client unblinds the signature on their `output-address` and transmits their signed, unblinded `output-address` to the mix operator.


Phase Three
-----------

0. The mix operator constructs a bitcoin transaction taking each `input-address` as an input, and assigning each `output-address` the `denomination` as an output.
1. The mix operator publishes this transaction.
2. Each client signs the transaction with the key for their `input-address`, and returns this to the mix operator.
3. Once every client has signed the transaction, the mix operator publishes this complete transaction to the bitcoin network.
4. If some clients fail to sign the transaction by the end of phase three, the mix operator may return the fee to each client which did sign the transaction.
