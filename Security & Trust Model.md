
Security & Trust Model
NameTensor minimizes trust assumptions.

Ownership and resolution are derived from finalized on-chain events. No server, API, or operator defines ownership.

The primary security assumption is control of private keys. If keys are lost, ownership cannot be recovered.

There is no administrative override or recovery mechanism. These constraints are intentional and ensure permanent, self-sovereign ownership.

Operator Safety: Guardrails
NameTensor distinguishes between protocol rules and operator guardrails.

Protocol rules are permanent. They define how registrations are validated, how fees are enforced, and how ownership is determined. These rules exist in the indexer and are never removed.

Operator guardrails are safety mechanisms that prevent premature or accidental actions.

Permanent Safety Posture
Write operations always require explicit opt-in. The system is read-only by default. This prevents accidental writes in staging environments, forks, or misconfigured deployments.
Indexer validation is permanent. Fee verification, treasury destination checks, and ownership enforcement are protocol rules.
The default posture is safe. Writes are a deliberate decision, never an automatic state.
