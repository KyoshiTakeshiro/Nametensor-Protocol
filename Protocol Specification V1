
**Protocol Specification v1**

This document defines the canonical constants and rules that constitute NameTensor Protocol Version 1. Independent indexers must implement these rules identically to produce deterministic state from the same chain history.

**Protocol Version
Version: 1**

All on-chain remarks must include v: 1. Indexers must ignore remarks with unsupported version numbers.

**Canonical Treasury Address**
5ECkFbm4vkDZCuQFehUAcGT6wPSnzVXrXC4SjYXqsJ58KLcs
The treasury address is a protocol-level constant in v1. Any change requires a protocol version increment.

The NAMETENSOR_TREASURY_ADDRESS environment variable exists as a deployment safeguard and must match this constant exactly. If a mismatch is detected, the indexer will refuse to start.

**Fee Schedule**
All fees are denominated in planck (1 TAO = 1,000,000,000 planck). The transferred amount must match exactly — no overpayment, no underpayment.

Name Length	Fee (TAO)	Fee (planck)
1 character	2.5	2,500,000,000
2 characters	1.0	1,000,000,000
3 characters	0.5	500,000,000
4 characters	0.25	250,000,000
5+ characters	0.05	50,000,000
Sub-entry	0.01	10,000,000
Transfers require a zero-value transfer to the treasury address.

**Label Rules**
Root labels: ^[a-z0-9]{1,20}$

Lowercase alphanumeric only
1 to 20 characters
No dots, hyphens, or special characters
Sub-entries: ^[a-z0-9]{1,20}\.[a-z0-9]{1,20}$

Format: child.parent
Single level only — nested sub-entries (a.b.c) are prohibited
Registrant must own the parent name
Required Extrinsic Structure
All protocol actions must be submitted as a utility.batchAll extrinsic containing exactly two calls:

balances.transferKeepAlive (or balances.transfer / balances.transferAllowDeath) — fee payment to treasury
system.remarkWithEvent (or system.remark) — protocol inscription
Structural requirements:

utility.batch is rejected — only batchAll guarantees atomic execution
Nested batch calls (batch-within-batch) are rejected
Exactly two inner calls are required — no more, no less
Any unrecognized call types cause the entire extrinsic to be ignored
Remark Payload Format
The remark must begin with the prefix NT: followed by a base64-encoded JSON payload.

**Registration:**

{
  "p": "NT",
  "v": 1,
  "a": "register",
  "l": "<label>"
}
Transfer:

{
  "p": "NT",
  "v": 1,
  "a": "transfer",
  "l": "<label>",
  "to": "<ss58_address>"
}
p must be "NT"
v must be 1
a must be "register" or "transfer"
l is lowercased before validation
to is required for transfers, must be a valid SS58 address
Address Normalization
All SS58 addresses are normalized to generic prefix 42 before comparison. This ensures addresses encoded with different network prefixes (e.g., Bittensor prefix 13058) are compared correctly.

**Deterministic Replay**
Any independent indexer implementing these rules against the same Subtensor chain history must produce identical state. The protocol is designed for full reproducibility:

All rules are deterministic functions of on-chain data
No external state or randomness influences validation
Block processing is strictly sequential
The treasury address is a constant, not a runtime parameter
Given identical chain history and protocol rules, two independent indexers will always agree on the complete set of name-to-address mappings.

Zero-Value Transfer Invariant
Protocol v1 transfer actions require a balances.transferKeepAlive call to the canonical treasury address with value 0. This is a runtime dependency: the Subtensor chain must accept zero-value transfers for the transfer action to function.

**Verification:**

Runtime: node-subtensor specVersion 377
Verified at block: 7,584,484
Date: 2026-02-20
Method: paymentInfo confirmed the utility.batchAll([transferKeepAlive(treasury, 0), remarkWithEvent(...)]) extrinsic is constructable and accepted by the runtime. Substrate's balances pallet treats zero-value transfers as no-ops — no balance mutation occurs, no minimum value check applies.
Result: PASS — zero-value transferKeepAlive is valid under Subtensor runtime.
If a future Subtensor runtime upgrade introduces a minimum transfer amount or rejects zero-value transfers, the transfer action will break and a protocol version increment will be required.

**Versioning Contract**
The protocol version is incremented when any of the following change:

**Treasury address**
Fee schedule (planck amounts)
Label format rules (root or sub-entry regex)
Transfer rules
Extrinsic validation logic
State machine transition rules
The protocol version is NOT incremented for:

Infrastructure changes (RPC failover, caching, monitoring)
API additions that do not alter protocol semantics
Code refactors or reorganization
Logging, alerting, or notification changes
