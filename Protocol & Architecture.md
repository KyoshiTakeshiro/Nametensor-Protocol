
**Protocol & Architecture**
NameTensor is a deterministic naming protocol built on Subtensor.

It does not rely on off-chain authority, mutable databases, or administrative controls. Ownership is derived directly from finalized chain history under fixed, publicly defined rules.

Core Protocol Guarantees
NameTensor enforces the following invariants:

Permanent names — No renewals, no expiry. Once registered, a name exists permanently unless transferred.
Fixed fees — Registration costs are protocol-defined and length-based. Fees are not adjustable per user and cannot be modified silently.
Ownership from block history — State is derived exclusively from finalized Subtensor blocks.
No admin control — The backend cannot mint, edit, revoke, or alter names or fees.
Fully verifiable — Anyone can independently reconstruct identical state from chain history.
These guarantees are protocol properties, not UI promises.

Deterministic Indexing
NameTensor state is derived exclusively from finalized Subtensor blocks.

The indexer does not create ownership.
It deterministically reconstructs ownership by processing on-chain events.

Only finalized blocks are processed.
Unfinalized blocks are ignored to prevent transient state divergence.

Indexer Responsibilities
An indexer must:

Process finalized blocks in strict ascending order
Persist the last processed block height
Detect and recover from reorgs
Re-validate extrinsics during replay
Apply protocol validation rules consistently
Apply deterministic state transitions
No external coordination is required.

Deterministic State Reconstruction
NameTensor can be rebuilt entirely from chain history.

To reconstruct state:

Connect to a Subtensor RPC endpoint
Start from genesis or a known checkpoint
Iterate finalized blocks sequentially
Extract relevant balances.transfer* and system.remark* extrinsics
Decode remarks prefixed with "NT:"
Parse the base64 JSON payload
Apply protocol validation rules
Apply deterministic state transitions
If two independent indexers process the same finalized chain under identical rules, they will derive identical NameTensor state.

Independent Verification
Anyone can operate their own indexer to independently verify NameTensor state.

The public API is a convenience layer only.
The Subtensor chain is the canonical source of truth.

Verification guarantees:

No backend write authority

No hidden state

No privileged indexer

No silent overrides

Fully reproducible ownership mapping

NameTensor is a deterministic state machine over Subtensor history.
