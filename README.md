# IntentGuard

**Know what you sign.** A Solana devnet transaction-intent checker built for a hackathon demo.

## Run locally

Requires Node.js 22 or later, npm, and Python 3 (used to package the source ZIP).

```bash
npm ci
npm test
npm start
```

Open http://localhost:3000. The browser app is built when the server starts. Restart after source changes. All five demo scenarios work without a wallet, funds, or API key.

## Optional live AI explanations

Copy `.env.example` to `.env`, set your own `OPENAI_API_KEY`, and restart. `OPENAI_MODEL` defaults to `gpt-4.1-mini` and can be changed to a model available to your account. A ChatGPT subscription does not itself configure this key. Never paste a key into browser code, the submission, or a public repository. API usage may be billed by the provider.

Click **Explain with AI** after an inspection. The server independently re-decodes and verifies transaction bytes before sending the factual report and optional context to the model. Without a key, the UI explicitly uses rule-based explanations; it does not impersonate live AI. The verdict never depends on the model output. Avoid putting private information in the optional context field.

## Demo walkthrough (no wallet)

1. Keep the default intended payment of 0.01 SOL.
2. Select **Matching transfer**, then **Inspect transaction**. Expect MATCH.
3. Select **Amount inflated** and inspect. Expect MISMATCH: 0.1 SOL instead of 0.01 SOL.
4. Select **Recipient swapped** and inspect. Expect a recipient mismatch.
5. Select **Hidden extra transfer** and inspect. Expect an instruction-count mismatch and an extra transfer.
6. Select **Unsupported instruction** and inspect. Expect UNASSESSED; the app cannot approve unknown effects.
7. Export a report to get decoded instructions, the original bytes, and a SHA-256 digest of those bytes.

All scenarios construct actual serialized legacy transactions, then decode those bytes. They are not fabricated prewritten verdicts. The placeholder recent blockhash is for offline inspection only.

## Real devnet payment

1. Install the Phantom desktop browser extension and use a test wallet. Enable testnet/devnet display in the wallet if needed.
2. Obtain free devnet SOL from https://faucet.solana.com/ using your public wallet address.
3. Connect Phantom. Enter a recipient **you control**, select Matching transfer, and inspect again.
4. Click **Review & send on devnet**, check the full addresses and amount, then approve in your wallet.
5. The app refreshes the recent blockhash, rechecks intent, simulates on devnet, requests a signature, compares the signed message with the inspected message, and broadcasts only to the fixed devnet RPC endpoint.
6. Follow the explorer receipt for confirmation. Fees are additional to the intended amount.

The baked-in demo identities have publicly reproducible keys. They exist only for unsigned examples: never send real funds to them. No mainnet endpoint is configurable in this prototype. The app never requests a recovery phrase or private key.

## Import a transaction

Select **Import transaction**, paste base64-encoded serialized legacy Solana bytes, and enter the independently verified sender, recipient, and amount. The prototype supports exactly one native SOL `SystemProgram.transfer` instruction. Versioned transactions, SPL token transfers, swaps, approvals, transfer-with-seed, and additional programs are not supported. Unsupported or malformed payloads cannot be sent through this app. The send flow refreshes only the recent blockhash and revalidates the resulting message.

## Architecture

- `src/guard.js`: exact decimal-to-lamport conversion, transaction decoding, deterministic policy.
- `src/app.js`: scenarios, imports, verdict UI, report export, wallet signing, devnet flow.
- `src/server.js`: same-origin API, fixed devnet RPC proxy, optional server-side AI.
- `public/`: accessible responsive interface and styles.
- `scripts/build.mjs`: bundles browser/server and creates the downloadable ZIP.
- `scripts/dev.mjs`: dependency-light local HTTP adapter for the Worker.
- `tests/`: core and server behavior tests.
- `SUBMISSION.md`: submission draft and a two-minute demo script.

## Build and deployment

```bash
npm run build
npm run validate
```

The generated server is a Cloudflare Worker ES module at `dist/server/index.js`; its HTML, CSS, browser JavaScript, and source ZIP are embedded assets. For a separate Cloudflare account, create a local `.openai/hosting.json` containing `{}` if absent (running `npm start` does this automatically), run the build, and deploy with Wrangler using the included `wrangler.jsonc`. Configure `OPENAI_API_KEY` as a server secret, never in source. The privately hosted ChatGPT preview is owner-only; it is not a public judging URL.

## Verification and limits

Automated tests cover matching transactions, a one-lamport discrepancy, increased amount, wrong recipient/source/fee payer, extra transfers, unsupported instructions, malformed input, decimal precision, and server failure handling. See the actual `npm test` output for results. Synthetic tests are not a measured real-world scam-detection benchmark.

Wallet approval and a confirmed real devnet transfer require the user's wallet and test funds. They must be demonstrated before describing a confirmed end-to-end payment in a submission. Live AI also requires an API key and was not verified without one.

This is an intent checker, not a wallet-wide interceptor, block explorer, phishing detector, full audit, or proof that an address is trustworthy. Only transactions reviewed through IntentGuard are gated. An attacker could deceive a user about the intended address itself. Failures and unsupported programs are explicit. Review and harden authentication, rate limiting, provider costs, RPC quotas, and security before any public production use.

## Attribution

Built with AI assistance from ChatGPT/Codex. Uses `@solana/web3.js`, `buffer`, and `esbuild`; typography uses Google Fonts (DM Sans and Space Grotesk) with local system fallbacks. Original application code is MIT licensed. Dependency licenses remain with their authors. Learn and explain the code and follow your event's disclosure and submission requirements.
