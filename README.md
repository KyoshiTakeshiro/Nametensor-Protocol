
NameTensor

NameTensor is a deterministic, on-chain naming layer for the Bittensor ecosystem.

It provides permanent, human-readable names (e.g., satoshi) that resolve to TAO wallet addresses. Ownership is derived directly from the Subtensor chain. No backend authority can create, modify, or revoke names.

⸻

Architecture

NameTensor v1.1.0 is fully on-chain.

On-Chain Operations:

	•	Registration occurs directly via utility.batchAll extrinsic containing:
	•	balances.transferKeepAlive → sends the registration fee to the treasury
	•	system.remarkWithEvent → writes the NT protocol payload on-chain
	•	There is no backend write path.

Backend (Indexer) Role:

	•	Listens to finalized Subtensor blocks
	•	Parses valid NameTensor protocol remarks
	•	Validates fee and format rules
	•	Deterministically derives ownership
	•	Reconstructs state from chain history

If the database were deleted, it can be fully rebuilt from blockchain data.

⸻

Protocol Rules

Label Format

	•	Lowercase alphanumeric only
	•	Length: 1–20 characters
	•	No renewals
	•	First-come, first-served
	•	Permanent once registered

Fee Model (Root Names)

Length       Fee (TAO)
1            2.5
2            1.0
3            0.5
4            0.25
5+           0.05

A small inscription fee is added for the remark payload.

Fees are sent directly to the project treasury on-chain.

API (Read-Only)

Resolve a Name
GET /api/resolve/:name

Example:

curl https://nametensor.io/api/resolve/satoshi

Response:

{ “name”: “satoshi”, “address”: “5HbG…Wallet” }

Get Primary Name

GET /api/primary/:address

Returns the primary registered name for a wallet, if set.

⸻

Example Use Cases:

	•	Resolve a wallet address for a known name:
	
curl https://nametensor.io/api/resolve/alice

	•	Find the primary name associated with your wallet:
	
curl https://nametensor.io/api/primary/5CswvK7Mp5ZoEsxqvetkCvgNx9v5d4qmV9MtHjt8q8xBJYrA

	•	Build applications that display human-readable names instead of raw wallet addresses.

⸻

Development Setup

Install dependencies

• npm install

Push database schema

• npm run db:push

Start server

• npm run dev

	•	The indexer initializes from the current finalized block.
	•	To rewind or reset the indexer, use scripts in /script.

⸻

Tech Stack

	•	Frontend: React, Tailwind CSS
	•	Backend: Node.js, Express
	•	Indexer: Subtensor WebSocket via Polkadot API
	•	Database: PostgreSQL (Drizzle ORM)

⸻

Security Model

	•	No renewal logic
	•	No backend-controlled ownership
	•	No hidden state
	•	Deterministic reconstruction from chain history
	•	Treasury payments enforced at validation layer

On-Chain vs Backend:

All ownership and validation are derived from on-chain data. The backend only indexes and serves read-only API requests. It cannot alter ownership or registration.

NameTensor is designed to minimize trust assumptions while remaining simple and permanent.

⸻

Security

See docs/threat-model.md for a full architectural threat model covering frontend spoofing, RPC manipulation, indexer divergence, fee enforcement, and namespace abuse vectors.
