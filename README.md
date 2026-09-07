# MiniChain — A From-Scratch Blockchain & Cryptocurrency (Python, zero dependencies)

A working blockchain + cryptocurrency simulation built entirely with Python's standard
library, so it runs anywhere with no `pip install` needed.

## Goals of the System

| Goal | How it's achieved |
|---|---|
| **Decentralization** | Any node can hold a copy of `Blockchain` and independently verify it — no single source of truth is trusted blindly. |
| **Immutability** | Every block's hash depends on its content + the previous block's hash. Changing old data breaks the chain (`is_chain_valid()` catches it — demoed in `main.py`). |
| **Transparency** | All transactions live inside blocks in plain sight; `stats.py` acts like a block explorer. |
| **Security** | SHA-256 hashing, a Merkle root per block, and signed transactions (HMAC-based signature scheme) resist tampering and forged spends. |
| **Scarcity / Sound Economics** | Hard-capped supply (21,000,000 coins) + halving block reward every 20 blocks, mirroring Bitcoin's monetary policy. |
| **Stable Consensus** | Proof-of-Work with self-adjusting difficulty keeps average block time near a target instead of drifting as miners speed up/slow down. |

## File Structure

```
blockchain_project/
├── blockchain.py   # Block + Blockchain: mining, PoW, difficulty adjustment, validation
├── wallet.py        # Wallet (keypair) + Transaction (create/sign/validate)
├── stats.py         # Analytics: block explorer-style statistics
├── main.py          # End-to-end demo (run this)
└── README.md
```

## Core Functions

**`blockchain.py`**
- `Block.compute_hash()` — SHA-256 hash of a block's header fields
- `Block.compute_merkle_root()` — tamper-evident summary of all transactions in the block
- `Blockchain.add_transaction(tx)` — validates and queues a transaction into the mempool
- `Blockchain.mine_pending_transactions(miner_address)` — runs Proof-of-Work, pays the block reward, appends the new block
- `Blockchain.adjust_difficulty()` — retargets mining difficulty every 5 blocks based on actual vs. target block time
- `Blockchain.get_balance(address)` — replays the whole chain to compute a wallet's balance
- `Blockchain.is_chain_valid()` — walks the chain checking hashes, merkle roots, and links

**`wallet.py`**
- `Wallet()` — generates a private key + derives a public address
- `Wallet.sign(message)` — signs data with the private key
- `Transaction.sign_transaction(wallet)` / `Transaction.is_valid()` — sign and validate spends

**`stats.py`**
- `chain_stats(bc)` — total blocks/transactions, average block time, estimated hash rate, current reward, % of max supply mined, top balances, chain validity
- `print_stats(bc)` — pretty-prints the above like a block explorer dashboard

## Features

- Proof-of-Work mining with adjustable difficulty
- Self-correcting difficulty retarget (like Bitcoin's 2016-block retarget, scaled down for a demo)
- Wallets with signed transactions
- Mempool (pending transaction pool) with basic validation before inclusion
- Mining rewards with **halving** schedule + a **hard supply cap**
- Full chain validation / tamper detection
- Built-in stats/analytics module (mini block-explorer)
- Zero external dependencies — pure Python standard library

## Run It

```bash
python3 main.py
```

This will: create 4 wallets, mine several blocks, send transactions between wallets,
reject an invalid transaction, print final balances + network stats, then deliberately
tamper with a mined block to prove the chain detects it (`chain_valid` flips to `False`).

## Ideas for Extending This Further

- Swap the HMAC-based signing for real ECDSA/EdDSA (`ecdsa` or `cryptography` library) for production-grade cryptographic signatures
- Add a P2P networking layer (sockets or `asyncio`) so multiple nodes can gossip blocks and reach consensus for real, including a longest-chain conflict-resolution rule
- Add transaction fees that go to the miner (partially wired in already via `Transaction.fee`)
- Persist the chain to disk (JSON or SQLite) instead of keeping it in memory
- Build a REST API (Flask/FastAPI) on top so a web wallet or block explorer UI can talk to a running node
- Add a Merkle proof endpoint so light clients can verify a single transaction without downloading the whole chain
- Move from Proof-of-Work to Proof-of-Stake for lower energy use

## Important Note

This is an **educational simulation**, not production-ready cryptography. The signature
scheme is simplified (HMAC keyed by a private key) rather than true asymmetric
ECDSA/EdDSA signatures. Do not use this as-is to hold real value.
