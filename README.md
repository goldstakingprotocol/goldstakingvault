# $GOLD Vault Documentation

## Introduction

The $GOLD staking protocol is a decentralized vault system built on Solana that offers unprecedented 10% compound interest every 7 minutes. By locking $GOLD tokens in our smart contract, users reduce circulating supply while earning exponential rewards.

## How It Works

**What happens when you stake:** Your $GOLD tokens are locked in a secure vault on the blockchain. Think of it like a high-yield savings account, but much more powerful.

**Supply and demand economics:** When you stake, those tokens are removed from circulation. Less $GOLD available to trade = higher price for everyone. It's simple economics - scarcity drives value.

**Compound interest magic:** Every 7 minutes, your stake grows by 10%. This isn't simple interest - it's compound. Your rewards earn rewards, creating exponential growth.

**Example:** Stake 1,000 $GOLD. After 7 minutes: 1,100. After 14 minutes: 1,210. After 1 hour: ~5,743. The growth accelerates rapidly!

## Technical Architecture

### Smart Contract Architecture

**Program ID:** FZ7rgdWAfFDHZKV4NYRkxDgZiXUPe5rEqZ3vCk5VKu6Z  
**Network:** Solana Mainnet

**Key Instructions:**
- `create_gold_vault`: Initialize vault with token mint
- `deposit_gold`: Stake tokens (locks in PDA)
- `withdraw_gold`: Claim stake + rewards

**Account Structure:**
- Vault PDA: Holds staked tokens
- User Interactions PDA: Tracks 5 stake positions per user
- Position Data: amount, timestamp, index (0-4)

### Compound Interest Calculation

```rust
// On-chain calculation (Rust)
let minutes_staked = (current_time - stake_time) / 60;
let periods = minutes_staked / 7; // 7-minute periods
let compound_rate = 1.10; // 10% growth
let final_amount = initial_amount * compound_rate.pow(periods);
```

```typescript
// Frontend calculation (TypeScript)
const calculateRewards = (stake: StakePosition): number => {
  const minutesStaked = (Date.now() - stake.timestamp) / (1000 * 60);
  const periods = Math.floor(minutesStaked / 7);
  const compoundRate = 0.10; // 10%
  return stake.amount * Math.pow(1 + compoundRate, periods) - stake.amount;
};
```

### Transaction Flow

1. Frontend calls Supabase edge function with stake parameters
2. Edge function builds Solana transaction using @solana/web3.js
3. Transaction includes SPL token transfer to vault PDA
4. Smart contract updates user's position data on-chain
5. Frontend signs transaction with wallet (Phantom/Solflare)
6. Transaction submitted to Solana network
7. Confirmation triggers UI update and balance refresh

## Security Features

**On-chain verification:** All calculations happen on Solana blockchain. No centralized server can manipulate your rewards.

**PDA (Program Derived Address):** Tokens are held in deterministic addresses. Only the smart contract can move funds, ensuring safety.

**Time-based security:** Blockchain timestamps prevent manipulation. Each stake's growth is cryptographically verifiable.

**Open source:** Contract code is public and auditable. Community can verify the logic anytime.

## Economic Model

**Deflationary pressure:** Staking removes $GOLD from circulation. As more users stake, available supply decreases, naturally increasing token value.

**Reward sustainability:** The 400M $GOLD vault ensures long-term rewards. High APY attracts stakers, reducing sell pressure.

**Network effects:** More stakers → Less supply → Higher price → More attractive to stake. This creates a positive feedback loop benefiting all participants.

**Market dynamics:** Unlike traditional staking, our model directly impacts token economics. Every stake is a vote of confidence that strengthens the ecosystem.

## Integration Guide

### Quick Start

```javascript
// 1. Connect wallet
const wallet = window.solana;
await wallet.connect();

// 2. Call stake function
const { data } = await supabase.functions.invoke('stake-tokens', {
  body: {
    userWallet: wallet.publicKey.toString(),
    amount: 1000, // Amount in tokens
    index: 0 // Position 0-4
  }
});

// 3. Sign and send transaction
const transaction = Transaction.from(bs58.decode(data.transaction));
const signed = await wallet.signTransaction(transaction);
const signature = await connection.sendRawTransaction(signed.serialize());
```

## Frequently Asked Questions

**Is the 10% every 7 minutes sustainable?**

Yes. The 400M $GOLD reward pool and deflationary tokenomics ensure long-term sustainability.

**What's the minimum stake period?**

7 minutes. You can claim your original stake plus rewards after just one compound period.

**Why 5 position slots?**

This allows strategic staking at different times while keeping the contract efficient and gas-optimized.

**Can I lose my staked tokens?**

No. The smart contract guarantees return of your principal plus accumulated rewards. Only you can withdraw your stake.