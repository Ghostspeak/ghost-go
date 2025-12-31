# GhostSpeak CLI Improvement Plan

## Executive Summary

The current CLI is well-structured but **90% of blockchain functionality is stubbed**. This document outlines improvements needed for production readiness, developer experience, and extensibility.

---

## 🚨 Priority 1: Core Functionality (CRITICAL)

### 1.1 Implement Real Blockchain Transactions

**Current State:**
- All staking, governance, reputation operations return fake data
- 30+ "TODO: Build and submit Solana transaction" comments
- Users can't actually use the CLI for real operations

**Required:**

```go
// Create transaction builder interface
type TransactionBuilder interface {
    BuildStakeTransaction(params StakeParams) (*solana.Transaction, error)
    BuildUnstakeTransaction(params UnstakeParams) (*solana.Transaction, error)
    BuildClaimRewardsTransaction(params ClaimParams) (*solana.Transaction, error)
}

// Implement using actual Solana program IDL
type GhostSpeakTransactionBuilder struct {
    programID solana.PublicKey
    client    *solana.Client
}

func (b *GhostSpeakTransactionBuilder) BuildStakeTransaction(params StakeParams) (*solana.Transaction, error) {
    // 1. Create instruction data using actual program IDL
    // 2. Derive PDA for staking account
    // 3. Build transaction with proper accounts
    // 4. Add compute budget if needed
    // 5. Return signed transaction
}
```

**Files to Update:**
- `pkg/solana/instructions.go` (NEW - instruction builders)
- `pkg/solana/accounts.go` (NEW - PDA derivation)
- `internal/services/staking.go` (replace TODO with real implementation)
- `internal/services/reputation.go`
- `internal/services/governance.go`
- `internal/services/escrow.go`

**Estimated Effort:** 2-3 weeks

---

### 1.2 Align with Actual Smart Contract

**Problem:** CLI assumes certain program structure that may not match reality.

**Required:**
1. **Get the actual Solana program IDL** from the smart contract team
2. Generate Go types from IDL (use `github.com/gagliardetto/anchor-go`)
3. Update domain models to match on-chain account structures
4. Implement proper account deserialization

**Example:**
```go
// pkg/solana/types.go (GENERATED FROM IDL)
type StakingAccountData struct {
    Staker           solana.PublicKey
    AmountStaked     uint64
    StakedAt         int64
    UnlockAt         int64
    Tier             uint8
    ReputationBoost  uint16  // Basis points
    IsActive         bool
}

func (s *StakingAccountData) Deserialize(data []byte) error {
    // Use borsh deserialization matching Rust program
}
```

---

### 1.3 Real Data Persistence

**Current:** Everything cached in BadgerDB for 5 minutes, then lost.

**Required:**
- Sync with Convex database (same as web app)
- OR: Implement proper local DB with migrations
- OR: Make CLI stateless and always query blockchain

**Recommended Approach:**

```go
// internal/ports/backend.go (NEW)
type Backend interface {
    // Staking
    GetStakingAccount(address string) (*StakingAccount, error)
    SaveStakingEvent(event StakingEvent) error

    // Reputation
    GetReputationScore(address string) (*GhostScore, error)
    UpdateReputationScore(address string, score GhostScore) error

    // Sync
    SyncWithBlockchain() error
}

// Two implementations:
// 1. ConvexBackend - talks to web app's Convex DB
// 2. LocalBackend - uses embedded SQLite
```

---

## 🎯 Priority 2: Developer Experience (HIGH)

### 2.1 Clean Up Type System

**Problem:** `TierBronze` (GhostScoreTier) vs `StakingTierBronze` (StakingTier) is confusing.

**Solution:**

```go
// internal/domain/tiers.go (NEW - centralize all tier definitions)

// ReputationTier for Ghost Score system
type ReputationTier string
const (
    ReputationBronze   ReputationTier = "Bronze"   // 0-399
    ReputationSilver   ReputationTier = "Silver"   // 400-599
    ReputationGold     ReputationTier = "Gold"     // 600-799
    ReputationPlatinum ReputationTier = "Platinum" // 800-1000
)

// StakingTier for token staking benefits
type StakingTier string
const (
    StakingBronze StakingTier = "Bronze" // 1K-9.9K GHOST
    StakingSilver StakingTier = "Silver" // 10K-99.9K GHOST
    StakingGold   StakingTier = "Gold"   // 100K+ GHOST
)

// No more naming conflicts!
```

**Update everywhere:**
- Rename `GhostScoreTier` → `ReputationTier`
- Rename constants: `TierBronze` → `ReputationBronze`
- Keep staking tiers as `StakingBronze` (no prefix needed)

---

### 2.2 Remove Half-Implemented Features

**Option A: Implement Lock Periods Properly**
```go
// If lock periods actually affect APY:
func CalculateAPYWithLockBonus(tier StakingTier, lockPeriod LockPeriod) float64 {
    baseAPY := getBaseAPYForTier(tier)
    lockBonus := getLockBonus(lockPeriod)  // 0%, 5%, 10%, 20%
    return baseAPY + lockBonus
}

// Remove the forced LockNone in cmd/staking.go
```

**Option B: Remove Lock Periods Entirely** (Recommended if not in smart contract)
```go
// Delete from domain:
- LockPeriod type
- Lock30Days, Lock90Days, Lock1Year constants
- GetLockPeriodDuration()
- GetLockAPYBonus()

// Simplify StakingAccount:
type StakingAccount struct {
    // Remove:
    // LockPeriod   LockPeriod
    // UnlocksAt    time.Time
}
```

**Decision needed:** Check with smart contract team - do lock periods exist on-chain?

---

### 2.3 Comprehensive Examples

**Create `examples/` directory:**

```
examples/
├── basic-staking/
│   ├── main.go          # Simple stake/unstake example
│   └── README.md
├── reputation-tracking/
│   ├── main.go          # Track agent reputation
│   └── README.md
├── custom-rewards/
│   ├── main.go          # Custom APY calculator
│   └── README.md
├── batch-operations/
│   ├── main.go          # Batch stake multiple wallets
│   └── README.md
└── integration-test/
    ├── main.go          # End-to-end test
    └── README.md
```

**Example:**
```go
// examples/basic-staking/main.go
package main

import (
    "github.com/ghostspeak/ghost-go/pkg/ghostspeak"
)

func main() {
    // Initialize client
    client, err := ghostspeak.NewClient(ghostspeak.Devnet)
    if err != nil {
        panic(err)
    }

    // Load wallet
    wallet, err := client.LoadWallet("my-wallet", "password")
    if err != nil {
        panic(err)
    }

    // Stake 5000 GHOST
    tx, err := client.Stake(wallet, 5000.0)
    if err != nil {
        panic(err)
    }

    fmt.Printf("Staked! Tx: %s\n", tx.Signature)
}
```

---

## 🔌 Priority 3: Extensibility (MEDIUM)

### 3.1 Define Core Interfaces

**Create `internal/ports/` with all extension points:**

```go
// internal/ports/staking.go
type StakingProvider interface {
    Stake(ctx context.Context, params StakeParams) (*StakingAccount, error)
    Unstake(ctx context.Context, params UnstakeParams) error
    GetAccount(ctx context.Context, address string) (*StakingAccount, error)
    CalculateRewards(ctx context.Context, account *StakingAccount) (uint64, error)
}

// internal/ports/reputation.go
type ReputationCalculator interface {
    CalculateScore(ctx context.Context, metrics ReputationMetrics) (int, error)
    DetermineTier(ctx context.Context, score int) (ReputationTier, error)
    ApplyBoosts(ctx context.Context, baseScore int, boosts []Boost) (int, error)
}

// internal/ports/transaction.go
type TransactionSigner interface {
    Sign(ctx context.Context, tx *solana.Transaction) error
    SignAll(ctx context.Context, txs []*solana.Transaction) error
}

type TransactionSubmitter interface {
    Submit(ctx context.Context, tx *solana.Transaction) (solana.Signature, error)
    SubmitBatch(ctx context.Context, txs []*solana.Transaction) ([]solana.Signature, error)
}
```

**Benefits:**
- Builders can swap implementations (e.g., custom reputation scoring)
- Easy to mock for testing
- Clear contracts between layers

---

### 3.2 Plugin System

**Create plugin architecture:**

```go
// pkg/plugins/plugin.go
type Plugin interface {
    Name() string
    Version() string
    Initialize(ctx context.Context, app *app.App) error
    Shutdown(ctx context.Context) error
}

// Example custom plugin:
type CustomRewardsPlugin struct {
    calculator RewardsCalculator
}

func (p *CustomRewardsPlugin) Initialize(ctx context.Context, app *app.App) error {
    // Register custom rewards calculator
    app.StakingService.SetRewardsCalculator(p.calculator)
    return nil
}

// Usage:
app.RegisterPlugin(&CustomRewardsPlugin{
    calculator: &BoostBasedCalculator{multiplier: 1.5},
})
```

**Plugin Discovery:**
```bash
# Install plugins via Go modules
go get github.com/ghostspeak/plugins/premium-rewards

# Or load from directory
boo plugin install ./my-plugin
boo plugin list
boo plugin enable premium-rewards
```

---

### 3.3 SDK Package

**Create `pkg/ghostspeak/` as a standalone SDK:**

```go
// pkg/ghostspeak/client.go
package ghostspeak

type Client struct {
    solana   *solana.Client
    staking  StakingProvider
    reputation ReputationCalculator
}

func NewClient(network Network, opts ...Option) (*Client, error) {
    // Initialize with defaults, allow customization via options
}

// Options pattern for extensibility
type Option func(*Client) error

func WithCustomStaking(provider StakingProvider) Option {
    return func(c *Client) error {
        c.staking = provider
        return nil
    }
}

func WithCustomReputation(calc ReputationCalculator) Option {
    return func(c *Client) error {
        c.reputation = calc
        return nil
    }
}
```

**Usage by builders:**
```go
import "github.com/ghostspeak/ghost-go/pkg/ghostspeak"

// Standard client
client, _ := ghostspeak.NewClient(ghostspeak.Devnet)

// Customized client
client, _ := ghostspeak.NewClient(
    ghostspeak.Devnet,
    ghostspeak.WithCustomReputation(&MyScoring{}),
    ghostspeak.WithRateLimit(100), // Custom rate limiting
)
```

---

## 📚 Priority 4: Documentation (MEDIUM)

### 4.1 Builder's Guide

**Create `docs/BUILDERS_GUIDE.md`:**

```markdown
# GhostSpeak CLI Builder's Guide

## Architecture Overview

The CLI follows Clean Architecture principles:

```
cmd/          → User interface (CLI commands)
internal/
  ├─ app/     → Application setup
  ├─ domain/  → Business logic (pure Go, no dependencies)
  ├─ services/→ Use cases (coordinates domain + infra)
  └─ ports/   → **Extension points** ← START HERE
pkg/
  ├─ ghostspeak/ → **Public SDK** ← Use this for integrations
  └─ solana/     → Blockchain client
```

## Extension Points

### 1. Custom Staking Logic

Implement `StakingProvider` interface...

### 2. Custom Reputation Scoring

Implement `ReputationCalculator` interface...

### 3. Custom Transaction Building

Implement `TransactionBuilder` interface...

## Examples

See `examples/` directory for:
- Basic staking integration
- Custom reputation calculator
- Batch operations
- Testing strategies
```

---

### 4.2 API Reference

**Generate from code comments:**

```bash
# Use godoc to generate
godoc -http=:6060

# Or use pkgsite
go install golang.org/x/pkgsite/cmd/pkgsite@latest
pkgsite
```

**Ensure all exported types have doc comments:**
```go
// StakingProvider defines the interface for staking operations.
// Implement this interface to create custom staking backends.
//
// Example:
//   type MyStaking struct {}
//   func (m *MyStaking) Stake(ctx, params) (*StakingAccount, error) { ... }
type StakingProvider interface {
    ...
}
```

---

### 4.3 Integration Cookbook

**Create `docs/COOKBOOK.md`:**

```markdown
# Integration Cookbook

## Scenario 1: Add Discord Bot Integration

**Goal:** Allow users to stake via Discord commands

**Steps:**
1. Install the SDK: `go get github.com/ghostspeak/ghost-go/pkg/ghostspeak`
2. Create Discord bot client
3. Use GhostSpeak SDK for staking operations
4. Handle webhooks for transaction confirmations

**Code:**
```go
// See examples/discord-bot/
```

## Scenario 2: Custom APY Calculator

**Goal:** Different APY calculation based on token holding period

**Steps:**
1. Implement `RewardsCalculator` interface
2. Register with StakingService
3. Override default calculation

**Code:**
```go
// See examples/custom-rewards/
```
```

---

## 🎨 Priority 5: User Experience (LOW but important)

### 5.1 Better Error Messages

**Current:**
```
Error: failed to stake: not implemented
```

**Better:**
```
Error: Staking failed - blockchain integration not yet complete

This is a known issue. The CLI currently simulates staking operations.
To use real staking, please:
  1. Use the web app at https://ghostspeak.ai
  2. Wait for CLI v1.1.0 (ETA: Q2 2025)
  3. Track progress: https://github.com/ghostspeak/ghost-go/issues/42

For help: boo help staking
```

---

### 5.2 Progress Indicators

**For long-running operations:**

```go
// Use spinner from charmbracelet/bubbles
spinner := spinner.New()
spinner.Spinner = spinner.Dot
fmt.Printf("%s Submitting transaction to blockchain...\n", spinner.View())

// After confirmation:
fmt.Printf("✓ Transaction confirmed: %s\n", signature)
```

---

### 5.3 Consistent Help Text

**Audit all commands for:**
- Clear descriptions
- Usage examples
- Common error scenarios
- Links to docs

**Example:**
```bash
$ boo staking stake --help

Stake GHOST tokens to earn rewards and unlock tier benefits.

USAGE
  boo staking stake <amount> [flags]

EXAMPLES
  # Stake 5,000 GHOST tokens
  boo staking stake 5000

  # Stake with custom network
  boo staking stake 5000 --network mainnet

TIERS
  Bronze: 1,000 - 9,999 GHOST    → +5% reputation
  Silver: 10,000 - 99,999 GHOST  → +15% reputation + verified badge
  Gold: 100,000+ GHOST           → +15% reputation + premium benefits

REQUIREMENTS
  • Minimum stake: 1,000 GHOST
  • Active wallet required (use: boo wallet create)
  • Sufficient SOL for transaction fees (~0.001 SOL)

RESOURCES
  • Staking guide: https://docs.ghostspeak.ai/staking
  • APY calculator: https://ghostspeak.ai/calculator
  • Support: https://discord.gg/ghostspeak
```

---

## 📊 Implementation Roadmap

### Phase 1: Core Functionality (4-6 weeks)
- [ ] Get smart contract IDL
- [ ] Implement transaction builders
- [ ] Replace all TODO stubs with real implementations
- [ ] Integration testing with devnet

### Phase 2: Type System Cleanup (1-2 weeks)
- [ ] Rename GhostScoreTier → ReputationTier
- [ ] Decide on lock periods (keep or remove)
- [ ] Clean up inconsistencies

### Phase 3: Extension Points (2-3 weeks)
- [ ] Define all interfaces in `internal/ports/`
- [ ] Refactor services to use interfaces
- [ ] Create plugin system
- [ ] Build example plugins

### Phase 4: SDK Package (2-3 weeks)
- [ ] Extract `pkg/ghostspeak/` SDK
- [ ] Add options pattern
- [ ] Create integration examples
- [ ] Publish standalone module

### Phase 5: Documentation (2 weeks)
- [ ] Builder's Guide
- [ ] API Reference (godoc)
- [ ] Integration Cookbook
- [ ] Video tutorials

### Phase 6: Polish (1-2 weeks)
- [ ] Better error messages
- [ ] Progress indicators
- [ ] Help text audit
- [ ] UX improvements

**Total Estimated Time: 12-17 weeks**

---

## 🎯 Success Metrics

### For Users
- [ ] Can stake/unstake real GHOST tokens via CLI
- [ ] Reputation scores sync with web app
- [ ] Clear error messages when things fail
- [ ] Response time < 2s for most operations

### For Developers
- [ ] Can build custom plugin in < 1 hour
- [ ] Complete example for every major use case
- [ ] API reference covers 100% of public interfaces
- [ ] Contribution guide shows how to add new features

### For Builders
- [ ] Can integrate SDK into existing app in < 1 day
- [ ] Can customize staking/reputation logic without forking
- [ ] Clear extension points documented
- [ ] Active plugin ecosystem (5+ community plugins)

---

## 🤝 Getting Help

- GitHub Issues: https://github.com/ghostspeak/ghost-go/issues
- Discord: https://discord.gg/ghostspeak
- Documentation: https://docs.ghostspeak.ai

---

*Last updated: 2025-12-31*
