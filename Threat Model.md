
NameTensor Threat Model

1. Scope
NameTensor is a deterministic state machine that derives all registration state exclusively from finalized Subtensor (Bittensor) blocks.

The system:

Does not deploy or interact with smart contracts
Does not maintain privileged write paths
Does not create ownership off-chain
Serves only as a read-only index of on-chain events
Registration authority is established solely through on-chain extrinsics. The backend database represents derived state and can be independently reconstructed from chain history at any time.

This threat model considers attacks against:

Frontend
Backend
Indexer
Protocol validation rules
Economic incentives
Underlying chain dependencies
2. System Assumptions
NameTensor inherits its security guarantees from Subtensor. It does not introduce additional consensus mechanisms.

The system assumes:

Subtensor consensus remains secure.
Finalized blocks are economically irreversible under normal network conditions.
RPC endpoints eventually reflect canonical chain state.
SS58 address encoding remains stable.
utility.batchAll maintains atomic execution semantics.
balances.transferKeepAlive semantics remain consistent.
system.remarkWithEvent continues emitting events deterministically.
If these assumptions change due to runtime upgrades or network events, a protocol version increment and rule review may be required.

NameTensor does not increase consensus risk. It inherits Subtensor's security model.

3. Frontend Spoofing
Threat
An attacker deploys a phishing or modified frontend that:

Displays a fake treasury address
Alters displayed fee amounts
Misrepresents name availability
Tricks users into signing malicious transactions
Mitigations
Treasury enforcement in validator The indexer validates that the transfer destination matches the canonical treasury address. Payments to any other address are rejected regardless of frontend display.

Exact fee validation The validator computes required fees using BigInt arithmetic. The transferred amount must match exactly.

Wallet confirmation transparency Wallets display the full extrinsic details prior to signing. Users can verify:

Transfer destination
Amount
Batch structure
Remark payload
On-chain source of truth Registrations are valid only if they appear in finalized blocks and pass protocol validation.

A spoofed frontend cannot fabricate chain state.

4. RPC Manipulation
Threat
A malicious or compromised RPC endpoint may:

Serve fabricated blocks
Omit transactions
Present unfinalized blocks as finalized
Hide specific extrinsics
Mitigations
Finalized blocks only The indexer subscribes exclusively to finalized headers.

Multiple RPC endpoint support Comma-separated RPC endpoints with automatic failover reduce single-source dependency.

Deterministic rebuild State can be reconstructed from block zero or a known checkpoint using any trusted archive RPC.

Public reproducibility Any operator can run an independent indexer instance and compare derived state.

If RPC endpoints disagree, divergence is detectable.

5. Indexer Divergence
Threat
The indexer may diverge due to:

Reorg handling bugs
Parsing inconsistencies
Pruned block state
Database corruption
Software errors
Mitigations
Strict sequential processing Blocks are processed strictly in ascending order.

Reorg detection Parent hash mismatches trigger rollback to last known-good block.

Deterministic parsing Identical block input produces identical derived state.

Archive rebuild capability State can be rebuilt from earlier blocks using a full archive RPC.

Open-source verification Third parties can independently audit and replay the chain.

6. Fee Manipulation Attempts
Threat
An attacker attempts to:

Underpay required fees
Send funds to an alternate address
Inject malformed remarks
Use alternate transfer calls
Exploit batch partial execution
Mitigations
Exact BigInt fee match Required fee must match exactly at planck precision.

Exact normalized treasury match Treasury address comparison occurs after SS58 normalization.

Strict remark schema validation Payload must:

Contain "NT:" prefix
Conform to expected JSON schema
Match allowed actions
Pass label format rules
Protocol and policy separation Extrinsic structure validation is separated from economic validation.

Both must pass.

7. Batch Partial Execution Risk
Threat
Substrate's utility.batch does not guarantee atomic execution.

If:

transferKeepAlive fails
remarkWithEvent succeeds
The extrinsic status may still be "Success".

This previously allowed a registration without payment.

Mitigations
utility.batchAll enforcement Only utility.batchAll is accepted.

Atomic revert guarantee If any inner call fails, the entire extrinsic reverts. No remark is emitted.

Nested batch rejection Nested utility.batch or utility.batchAll calls are rejected.

Client-side enforcement Frontend uses utility.batchAll exclusively.

This class of exploit is eliminated.

8. Namespace Squatting
Threat
First-come-first-served registration allows:

Brand squatting
Short-name monopolization
Speculative bulk acquisition
Mitigations
Length-based fee curve Short names incur significantly higher fees.

Genesis pre-registration Certain ecosystem-relevant names were registered early to prevent squatting. They follow the same protocol rules as all other names.

Uniform rule enforcement No privileged or discounted registration path exists.

Tradeoff Acknowledgement
Names are permanent and non-expiring. Scarcity is permanent. There is no forced reclamation mechanism.

This is a deliberate design choice favoring stability over reclamation.

9. Backend Compromise
Threat
A compromised backend could:

Serve incorrect API responses
Display misleading availability
Corrupt local database
Mitigations
Backend cannot mutate protocol state All ownership derives from chain history.

Derived database only Database entries are reproducible from chain.

Independent verification possible Anyone can verify state by replaying finalized blocks.

Boundary Clarification
A compromised frontend or backend can mislead users, but cannot create valid on-chain registrations.

User wallets remain the final signing authority.

10. Economic Attack Surface
Threat
An attacker may:

Spam low-value names
Attempt large-scale namespace capture
Farm names for resale markets
Mitigations
Fee friction Every registration requires on-chain payment.

No refund design Fees are non-refundable and sunk upon finalization.

Chain throughput limits Each registration requires a separate finalized extrinsic.

Subtensor block production rate inherently limits throughput.

11. Runtime Upgrade Risk
NameTensor depends on behavior of:

balances pallet
system.remarkWithEvent
utility.batchAll
If a Subtensor runtime upgrade modifies:

Atomicity guarantees
Transfer semantics
Event emission rules
Encoding structure
Then protocol validation rules may require adjustment and protocolVersion increment.

Protocol changes are explicit and versioned. They are never silently modified.

Security Model Summary
NameTensor's security posture rests on:

Deterministic State Ownership is derived exclusively from finalized chain history.

Strict Validation Exact fee matching, normalized address enforcement, strict remark schema.

Atomic Execution utility.batchAll eliminates partial execution exploits.

No Off-Chain Authority Backend cannot mint, edit, or revoke names.

Public Reproducibility Anyone can rebuild identical state from chain history.

Transparent Treasury Routing Treasury address is enforced and verifiable on-chain.

Explicit Dependency Model Security inherits Subtensor consensus assumptions.
