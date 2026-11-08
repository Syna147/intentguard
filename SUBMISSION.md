# IntentGuard — Know what you sign

## Short description

IntentGuard compares the actual instructions in a Solana transaction with the payment a person intended, then explains discrepancies before they sign.

## Problem

A polished payment page is not proof that its transaction does what the user expects. Someone intending to send 0.01 SOL could be presented with a different amount, substituted recipient, or an appended transfer.

## What we built

A devnet prototype that decodes serialized legacy Solana transactions and compares them against an independently entered sender, recipient, and amount. It uses exact integer arithmetic, flags extra instructions, and refuses to approve unsupported effects. A Phantom signing flow rechecks the transaction, simulates it, verifies the signed message has not changed, and submits through a devnet-only endpoint.

An optional AI endpoint translates verified findings into plain language. Deterministic code controls the verdict and signing eligibility; the language model never authorizes a transaction. Without an API key, the app explicitly labels its explanation as rule-based.

## Why Solana

The prototype works with actual Solana transaction instructions and serialization. It can simulate and submit a native SOL transfer on devnet, with an explorer receipt. Blockchain functionality is the subject of verification rather than an unrelated record attached to a generic app.

## Technology

JavaScript, Solana web3.js, Phantom wallet integration, a Cloudflare-compatible Worker, optional OpenAI API, Node.js, esbuild, and Node's test runner.

## Scope and honest limitations

One native SOL transfer in a legacy transaction. No mainnet, SPL token approvals, swaps, versioned transactions, or general scam-detection claims. Matching intent does not prove recipient trustworthiness. Demo scenarios use real unsigned transaction bytes; they are not on-chain activity until signed and submitted. Automated synthetic tests do not establish real-world detection accuracy.

## AI disclosure

AI coding assistance was used to create and debug this project. The submitter should review, test, and understand the code before submitting. The optional model explains a server-verified report; it does not make the approval decision.

## Two-minute demo script

**0:00–0:20 — Problem**
“I want to send 0.01 SOL to this address. But the screen I clicked is not the same thing as the transaction my wallet signs. IntentGuard checks the actual instructions against my intent.”

**0:20–0:40 — Matching transaction**
Show Matching transfer and Inspect. Explain that the app decodes real transaction bytes and compares integer lamports and public keys.

**0:40–1:10 — Attack scenarios**
Show Recipient swapped and Amount inflated. Point to the expected and actual columns. Show Hidden extra transfer. Signing is disabled.

**1:10–1:25 — Uncertainty**
Show Unsupported instruction. “We do not label unknown effects safe. Unsupported instructions stop this flow.”

**1:25–1:40 — Explainability**
If a live API key is configured, click Explain with AI. Otherwise explicitly say, “This is the deterministic explanation; the live AI integration is implemented but not configured.” Do not describe fallback text as an AI response.

**1:40–2:00 — Proof and close**
If you have already tested a devnet transfer, show its actual explorer receipt. Otherwise show the exported report and say the wallet flow still needs end-to-end validation with your wallet. “Our goal is to make transaction intent inspectable, without letting AI decide what you sign.”

## Before submitting

- Replace any placeholder entrant/team details in the submission form.
- Confirm registration, eligibility, and the deadline with the event portal.
- Configure and demonstrate AI before claiming a working AI feature or selecting an AI track that requires it.
- Run `npm test` and follow the demo walkthrough.
- Test a real devnet transaction with your own test wallet if claiming confirmed transfers.
- Create a public GitHub repository if the event requires one; never include `.env` or keys.
- Record the required demo video yourself and include the actual deployed public URL if needed. The owner-only preview cannot be opened by judges.
- Describe implementation and validation accurately; no winning or security guarantees.
