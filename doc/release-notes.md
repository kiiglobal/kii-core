KII Core version 1.0.1.2
==========================

Release is now available from:

  <https://kii.global>

This is a new major version release, bringing new features, various bugfixes
and other improvements.

This release is mandatory for all nodes.

Please report bugs using the issue tracker at github:

  <https://github.com/kiiglobal/kii-core/issues>

Notable changes
===============

Opcode updates
--------------
Several opcodes have been reactivated/introduced to broaden the functionality
of the system and enable developers to build new solutions. These opcodes are
a combination of previously disabled ones that have been found to be safe and
new ones previously introduced by Bitcoin Cash. Details of the opcodes are
provided in DIP-0020.

These opcodes are activated via a BIP9 style hard fork that will begin
signalling on July 1st using bit 6. Any nodes that do not upgrade by the time
this feature is activated will diverge from the rest of the network.

DKG Data Sharing
----------------
Quorum resilience has been improved by enabling masternodes to request DKG data
from other quorum members. This allows KII Platform to obtain required
information while also making it possible for corrupted masternodes to recover
the DKG data they need to participate in quorums they are part of. Details are
provided in DIP-0021.

Platform support
----------------
Support for KII Platform has been expanded through the addition of a new
quorum type `LLMQ_100_67`, several RPCs, and a way to limit Platform RPC access
to a subset of allowed RPCs, specifically:
- `getbestblockhash`
- `getbestchainlock`
- `getblockcount`
- `getblockhash`
- `quorum sign` (for platform quorums only)
- `quorum verify`
- `verifyislock`

These changes provide necessary Platform capabilities while maximizing the
isolation between Core and Platform. New quorum type will be activated using
the same bit 6 introduced to activate new opcodes.

BLS update
----------
KII Core’s BLS signature library has been updated based on v1.0 of the
Chia BLS library to support migration to a new BLS signature scheme which will
be implemented in a future version of KII. These changes will be made to
align with standards and improve security.

Network performance improvements
--------------------------------
This version of KII Core includes multiple optimizations to the network and
p2p message handling code.

We reintroduced Intra-Quorum Connections which were temporary disabled with the introduction of
`SPORK_21_QUORUM_ALL_CONNECTED`. This should make communications for masternodes
belonging to the same quorum more robust and improve network connectivity in
general.

FreeBSD and MacOS machines will now default to the new `kqueue` network mode
which is similar to `epoll` mode on linux-based. It removes most of the CPU
overhead caused by the sub-optimal use of `select` in the network thread when
many connections were involved.

Wallet changes
--------------
Upgrading a non-Hierarchical Deterministic (HD) wallet to an HD wallet is now
possible. Previously new HD wallets could be created, but non-HD wallets could
not be upgraded. This update will enable existing non-HD wallets to upgrade to
take advantage of HD wallet features. Upgrades can be done via either the debug
console or command line and a new backup must be made when this upgrade is
performed.

Users can now load and unload wallets dynamically via RPC and GUI console.
It's now possible to have multiple wallets loaded at the same time, however only
one of them is going to be active and receive updates or be used for sending
trasactions at a specific point in time.

Also, enabling wallet encryption no longer requires a wallet restart.

Sporks
------
Several spork changes have been made to streamline code and improve system
reliability. Activation of `SPORK_22_PS_MORE_PARTICIPANTS` in KII v0.16
has rendered that spork unnecessary. The associated logic has been hardened
and the spork removed. `SPORK_21_QUORUM_ALL_CONNECTED` logic has been split
into two sporks, `SPORK_21_QUORUM_ALL_CONNECTED` and `SPORK_23_QUORUM_POSE`,
so that masternode quorum connectivity and quorum Proof of Service (PoSe) can
be controlled independently. Finally, `SPORK_2_INSTANTSEND_ENABLED` has a new
mode (value: 1) that enables a smooth transition in case InstantSend needs to
be disabled.

Statoshi backport
------------------
This version includes a backport of Statoshi functionality which allows
nodes to emit metrics to a StatsD instance. This can help node operators to
learn more about node performance and network state in general. We added
several command line options to give node operators more control, see options
starting with `-stats` prefix.

PrivateSend rename
------------------
PrivateSend has been renamed to CoinJoin to better reflect the functionality
it provides and align with industry standard terminology. The renaming only
applies to the UI and RPCs but does not change functionality.

Build system
------------
Multiple packaged in `depends` were updated. Current versions are:
- biplist 1.0.3
- bls-kii 1.0.1
- boost 1.70.0
- cmake 3.14.7
- expat 2.2.5
- mac alias 2.0.7
- miniupnpc 2.0.20180203
- qt 5.9.6
- zeromq 4.2.3

Packages libX11, libXext, xextproto and xtrans are no longer used.

Minimum supported macOS version was bumped to 10.10.

RPC changes
-----------
There are seven new RPC commands which are KII specific and seven new RPC
commands introduced through Bitcoin backports. One previously deprecated RPC,
`estimatefee`, was removed and several RPCs have been deprecated.

The new RPCs are:
- `createwallet`
- `getaddressesbylabel`
- `getaddressinfo`
- `getzmqnotifications`
- `gobject list-prepared`
- `listlabels`
- `masternode payments`
- `quorum getdata`
- `quorum verify`
- `signrawtransactionwithkey`
- `signrawtransactionwithwallet`
- `unloadwallet`
- `verifychainlock`
- `verifyislock`
- `upgradetohd`

The deprecated RPCs are all related to the deprecation of wallet accounts and
will be removed in KII v0.18. Note that the deprecation of wallet accounts
means that any RPCs that previously accepted an “account” parameter are
affected — please refer to the RPC help for details about specific RPCs.

The deprecated RPCs are:
- `getaccount`
- `getaccountaddress`
- `getaddressbyaccount`
- `getreceivedbyaccount`
- `listaccounts`
- `listreceivedbyaccount`
- `masternode current`
- `masternode winner`
- `move`
- `sendfrom`
- `setaccount`

`protx register` and `protx register_fund` RPCs now accept an additional `submit` parameter
which allows producing and printing ProRegTx-es without relaying them
to the network.

Also, please note that all mixing-related RPCs have been renamed to replace
“PrivateSend” with “CoinJoin” (e.g. `setprivatesendrounds` -> `setcoinjoinrounds`).

Please check `help <command>` for more detailed information on specific RPCs.

Command-line options
--------------------
Changes in existing cmd-line options:

New cmd-line options:
- `-dip8params`
- `-llmq-data-recovery`
- `-llmq-qvvec-sync`
- `-llmqinstantsend`
- `-platform-user`
- `-statsenabled`
- `-statshost`
- `-statshostname`
- `-statsport`
- `-statsns`
- `-statsperiod`
- `-zmqpubhashrecoveredsig`
- `-zmqpubrawrecoveredsig`

Also, please note that all mixing-related command-line options have been
renamed to replace “PrivateSend” with “CoinJoin” (e.g. `setprivatesendrounds`
-> `setcoinjoinrounds`).

Please check `Help -> Command-line options` in Qt wallet or `kiid --help` for
more information.

Miscellaneous
-------------
A lot of refactoring, code cleanups and other small fixes were done in this release.

Older releases
==============

KII Core tree 1.0.0.x was a fork of Dash Core tree 0.17.0.2,

KII Core tree 1.0.1.x was a fork of Dash Core tree 0.17.0.3.

