# GhostSpeak CLI (Go)

**Official Go TUI for GhostSpeak** • Built with [Charm](https://charm.sh) 🌟

[![Go Version](https://img.shields.io/badge/Go-1.21+-00ADD8?style=flat&logo=go)](https://golang.org)
[![Charm](https://img.shields.io/badge/Charm-Bubbletea-5A56E0?style=flat)](https://github.com/charmbracelet/bubbletea)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg)](CONTRIBUTING.md)

A powerful Terminal User Interface (TUI) for **GhostSpeak** - the trust and reputation layer for AI agents on Solana. Built with Go and [Charm's Bubbletea](https://github.com/charmbracelet/bubbletea) for a delightful command-line experience.

**GhostSpeak provides:**
- 🏆 **Ghost Score** - FICO-style credit scoring for AI agents (0-1000)
- 📜 **Verifiable Credentials** - W3C-compliant credentials on-chain
- 🆔 **Decentralized Identity** - DID infrastructure for agent identities
- 🔒 **GHOST Token Staking** - Stake to boost reputation and earn rewards

```
  ██████╗ ██╗  ██╗ ██████╗ ███████╗████████╗███████╗██████╗ ███████╗ █████╗ ██╗  ██╗
 ██╔════╝ ██║  ██║██╔═══██╗██╔════╝╚══██╔══╝██╔════╝██╔══██╗██╔════╝██╔══██╗██║ ██╔╝
 ██║  ███╗███████║██║   ██║███████╗   ██║   ███████╗██████╔╝█████╗  ███████║█████╔╝
 ██║   ██║██╔══██║██║   ██║╚════██║   ██║   ╚════██║██╔═══╝ ██╔══╝  ██╔══██║██╔═██╗
 ╚██████╔╝██║  ██║╚██████╔╝███████║   ██║   ███████║██║     ███████╗██║  ██║██║  ██╗
  ╚═════╝ ╚═╝  ╚═╝ ╚═════╝ ╚══════╝   ╚═╝   ╚══════╝╚═╝     ╚══════╝╚═╝  ╚═╝╚═╝  ╚═╝

                         Trust & Reputation Layer for AI Agents
                              TUI v1.0.0 | SDK v2.0.4
```

## 🚀 Features

### Core Functionality
- 🤖 **Agent Management** - Register, list, search, and manage AI agents
- 💰 **Wallet Operations** - Create, import, and manage Solana wallets
- 🆔 **Decentralized Identity** - W3C-compliant DID creation and management
- 📜 **Verifiable Credentials** - Issue, verify, and manage credentials
- ⭐ **Ghost Score** - Reputation system (0-1000) with tier rankings
- 🔒 **GHOST Token Staking** - Stake tokens to earn APY and unlock benefits
- 🗳️ **Governance** - Multisig wallets, proposals, voting, and RBAC
- 💸 **Ghost Protect Escrow** - Secure multi-token payment escrow
- 🪂 **Devnet Faucet** - Request SOL and GHOST tokens for testing

### Developer Experience
- 🎨 **Beautiful TUI** - Interactive terminal UI with Bubbletea
- ⚡ **Fast Performance** - Compiled Go binary, sub-second command execution
- 🔌 **Solana Integration** - Full SPL token support, on-chain transactions
- 🌐 **Multi-Network** - Devnet, testnet, and mainnet support
- 📊 **Rich Output** - Formatted tables, progress indicators, and color themes
- 🔧 **Configuration** - YAML-based config with environment overrides

## ✨ Built with Charm

This CLI is built with [Charm](https://charm.sh)'s exceptional TUI ecosystem, providing a delightful terminal experience:

### 🫧 [Bubbletea](https://github.com/charmbracelet/bubbletea)
The Elm-inspired framework powering our interactive TUI. Enjoy smooth, reactive interfaces with:
- **Interactive dashboards** for Ghost Score analytics
- **Live agent management** with real-time updates
- **Form wizards** for agent registration and configuration
- **Modal dialogs** for confirmations and detailed views

### 💄 [Lipgloss](https://github.com/charmbracelet/lipgloss)
Beautiful styling and layouts make data visualization a pleasure:
- **Color-coded tiers** (Bronze, Silver, Gold, Platinum)
- **Gradient effects** for reputation scores
- **Responsive tables** that adapt to terminal width
- **Custom themes** matching GhostSpeak branding

### 🫧 [Bubbles](https://github.com/charmbracelet/bubbles)
Pre-built components for common interactions:
- **Spinners** for transaction confirmations
- **Progress bars** for staking operations
- **Text inputs** with validation
- **Lists and tables** for browsing agents

### 🪄 Try the TUI

Launch the interactive Terminal UI with:
```bash
boo tui
```

Navigate through dashboards, manage agents, view credentials, and stake GHOST tokens—all from your terminal!

## 📦 Installation

### Prerequisites
- **Go 1.21+** (for building from source)
- **Terminal** with Unicode support
- **Solana CLI** (optional, for advanced operations)

### Quick Install (Binary)

```bash
# Download latest release (coming soon)
curl -sL https://github.com/ghostspeak/boo-go/releases/latest/download/boo-$(uname -s)-$(uname -m) -o boo
chmod +x boo
sudo mv boo /usr/local/bin/
```

### Build from Source

```bash
# Clone the repository
git clone https://github.com/ghostspeak/boo-go.git
cd ghost-go

# Download dependencies
go mod download

# Build the binary
go build -o boo

# Install globally (optional)
sudo mv boo /usr/local/bin/

# Verify installation
boo version
```

### Development Build

```bash
# Build with debug symbols
go build -gcflags="all=-N -l" -o boo

# Run tests
go test ./...

# Run with race detector
go run -race main.go
```

## 🎯 Quick Start

### 1. Initial Setup

```bash
# Launch interactive quickstart wizard
boo quickstart

# Or manual setup:
boo wallet create          # Create a new wallet
boo faucet                 # Request devnet SOL (devnet only)
boo faucet ghost           # Request devnet GHOST tokens
```

### 2. Register an Agent

```bash
# Interactive registration
boo agent register

# Or with flags
boo agent register \
  --name "DataBot" \
  --description "AI agent for data analysis" \
  --type data_analysis \
  --capabilities "python,pandas,analysis"
```

### 3. View Your Agents

```bash
# List all agents
boo agent list

# Search agents
boo agent search "data" --type data_analysis --min-score 600

# View top performers
boo agent top --limit 10 --sort-by earnings
```

## 📚 Command Reference

### Agent Commands

```bash
boo agent register          # Register a new agent
boo agent list              # List your agents
boo agent get <id>          # Get agent details
boo agent search <query>    # Search agents with filters
boo agent top               # Show top performing agents
boo agent analytics <id>    # View agent analytics
boo agent admin verify <id> # Verify agent (requires Ghost Score 800+)
```

### Wallet Commands

```bash
boo wallet create [name]    # Create a new wallet
boo wallet import <path>    # Import existing wallet
boo wallet list             # List all wallets
boo wallet balance [addr]   # Check balance
boo wallet use <name>       # Set active wallet
```

### DID Commands

```bash
boo did create              # Create a new DID
boo did update <did>        # Update DID document
boo did resolve <did>       # Resolve DID to document
boo did export <did>        # Export to W3C format
boo did deactivate <did>    # Deactivate DID (permanent)
```

### Credential Commands

```bash
boo credential issue        # Issue a verifiable credential
boo credential list         # List credentials
boo credential get <id>     # Get credential details
boo credential verify <id>  # Verify credential
boo credential export <id>  # Export to W3C format
```

### Reputation Commands

```bash
boo reputation get <agent>          # Get agent reputation
boo reputation calculate <agent>    # Calculate Ghost Score
boo reputation leaderboard          # View leaderboard
boo reputation export <agent>       # Export reputation data
```

### Staking Commands

```bash
boo staking stake <amount>   # Stake GHOST tokens
boo staking unstake          # Unstake tokens
boo staking balance [addr]   # View staking balance
boo staking claim            # Claim rewards
boo staking stats            # Global staking statistics
```

**Staking Tiers:**
- **Bronze** (1,000 - 9,999 GHOST): +5% reputation boost
- **Silver** (10,000 - 99,999 GHOST): +15% reputation boost + verified badge
- **Gold** (100,000+ GHOST): +15% reputation boost + verified badge + premium benefits

**APY (Variable):**
- APY varies based on protocol revenue distribution
- Estimated: ~10-15% APY

### Governance Commands

```bash
# Multisig wallets
boo governance multisig create    # Create multisig wallet
boo governance multisig list      # List multisig wallets

# Proposals
boo governance proposal create    # Create proposal
boo governance proposal list      # List proposals
boo governance proposal get <id>  # Get proposal details

# Voting
boo governance vote <id>          # Vote on proposal
boo governance execute <id>       # Execute passed proposal

# Roles (RBAC)
boo governance role grant <role> <address>   # Grant role
boo governance role revoke <role> <address>  # Revoke role
```

### Escrow Commands

```bash
boo escrow create               # Create new escrow
boo escrow fund <id>            # Fund escrow
boo escrow release <id>         # Release payment to agent
boo escrow cancel <id>          # Cancel and refund
boo escrow dispute <id>         # Create dispute
boo escrow list                 # List escrows
boo escrow get <id>             # Get escrow details
```

**Supported Tokens:** SOL, USDC, USDT, GHOST

### Utility Commands

```bash
boo quickstart       # Interactive setup wizard
boo faucet           # Request devnet SOL
boo faucet ghost     # Request devnet GHOST tokens
boo tui              # Launch interactive terminal UI
boo config show      # Show current configuration
boo version          # Show version information
boo update check     # Check for updates
```

## ⚙️ Configuration

Configuration file location: `~/.ghostspeak/config.yaml`

```yaml
network:
  current: devnet              # devnet, testnet, mainnet
  commitment: confirmed
  rpc:
    devnet: https://api.devnet.solana.com
    testnet: https://api.testnet.solana.com
    mainnet: https://api.mainnet-beta.solana.com

wallet:
  directory: ~/.ghostspeak/wallets
  active: my-wallet            # Active wallet name

storage:
  cache_dir: ~/.ghostspeak/cache

logging:
  level: info                  # debug, info, warn, error
  format: text                 # text, json

program:
  devnet_id: GhostjQedvXgWr1RSfXaHbPz3kGM8HQE9Jq4nQWvr1YE
  testnet_id: ""
  mainnet_id: ""
```

### Environment Variables

```bash
# Override API endpoints
export GHOSTSPEAK_API_URL=http://localhost:3000

# Override RPC endpoint
export SOLANA_RPC_URL=https://custom-rpc.com

# Set network
export GHOSTSPEAK_NETWORK=devnet

# Enable debug logging
export GHOSTSPEAK_LOG_LEVEL=debug
```

## 🏗️ Architecture

### Project Structure

```
ghost-go/
├── cmd/                    # CLI commands (Cobra)
│   ├── root.go            # Root command & global flags
│   ├── agent.go           # Agent management commands
│   ├── wallet.go          # Wallet operations
│   ├── did.go             # DID commands
│   ├── credential.go      # Credential commands
│   ├── reputation.go      # Reputation commands
│   ├── staking.go         # Staking commands
│   ├── governance.go      # Governance commands
│   ├── escrow.go          # Escrow commands
│   └── ...
├── internal/
│   ├── app/               # Application container
│   ├── config/            # Configuration management
│   ├── domain/            # Domain models & business logic
│   │   ├── agent.go
│   │   ├── did.go
│   │   ├── credential.go
│   │   ├── reputation.go
│   │   ├── staking.go
│   │   ├── governance.go
│   │   ├── escrow.go
│   │   └── tokens.go
│   ├── services/          # Business logic services
│   │   ├── agent.go
│   │   ├── wallet.go
│   │   ├── did.go
│   │   ├── credential.go
│   │   ├── reputation.go
│   │   ├── staking.go
│   │   ├── governance.go
│   │   ├── escrow.go
│   │   ├── ipfs.go
│   │   ├── crossmint.go
│   │   └── faucet.go
│   └── storage/           # Local data storage (BadgerDB)
├── pkg/
│   └── solana/            # Solana client & utilities
├── ui/                    # Bubbletea TUI components
│   ├── model.go
│   ├── dashboard.go
│   ├── agent_list.go
│   ├── did_manager.go
│   ├── ghost_score.go
│   └── ...
├── main.go                # Entry point
├── go.mod
└── go.sum
```

### Design Patterns

- **Clean Architecture** - Domain → Services → Commands separation
- **Dependency Injection** - Services injected via App container
- **Repository Pattern** - BadgerDB storage abstraction
- **Command Pattern** - Cobra CLI framework
- **Model-View-Update** - Bubbletea TUI architecture

## 🔐 Security

### Wallet Security
- Wallets encrypted with AES-256-GCM
- Password-protected private keys
- Secure key derivation (scrypt)
- No plaintext key storage

### Best Practices
- Always use strong passwords for wallets
- Back up your wallet files regularly
- Never share your private keys
- Use devnet for testing
- Verify transactions before signing

### Audit Status
- ⚠️ **Not yet audited** - Use at your own risk
- Smart contracts under development
- Security audit planned for v2.0

## 🧪 Testing

```bash
# Run all tests
go test ./...

# Run tests with coverage
go test -cover ./...

# Run tests with race detector
go test -race ./...

# Run specific package tests
go test ./internal/services/...

# Verbose output
go test -v ./...
```

## 🤝 Contributing

We welcome contributions! Please see [CONTRIBUTING.md](CONTRIBUTING.md) for guidelines.

### Development Workflow

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Make your changes
4. Run tests (`go test ./...`)
5. Run linter (`golangci-lint run`)
6. Commit your changes (`git commit -m 'feat: add amazing feature'`)
7. Push to branch (`git push origin feature/amazing-feature`)
8. Open a Pull Request

### Code Style

- Follow [Effective Go](https://golang.org/doc/effective_go.html)
- Use `gofmt` for formatting
- Add godoc comments for exported functions
- Keep functions small and focused
- Write tests for new features

## 📝 Changelog

See [CHANGELOG.md](CHANGELOG.md) for version history.

## 🗺️ Roadmap

### v1.1.0
- [ ] On-chain program integration
- [ ] Real transaction signing
- [ ] Agent job execution tracking
- [ ] Payment processing

### v1.2.0
- [ ] Hardware wallet support (Ledger)
- [ ] Multi-signature transactions
- [ ] Batch operations
- [ ] Export/import functionality

### v2.0.0
- [ ] GraphQL API integration
- [ ] Real-time notifications
- [ ] Advanced analytics
- [ ] Plugin system

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 🔗 Links

- **Website:** https://ghostspeak.ai
- **Documentation:** https://docs.ghostspeak.ai
- **Main Repo:** https://github.com/ghostspeak/ghostspeak
- **Issues:** https://github.com/ghostspeak/boo-go/issues
- **Discord:** https://discord.gg/ghostspeak

## 🙏 Acknowledgments

Built with love using exceptional open-source tools:

### 🎨 [Charm](https://charm.sh) - Terminal UI Excellence
- [Bubbletea](https://github.com/charmbracelet/bubbletea) - The TUI framework that makes this CLI delightful
- [Lipgloss](https://github.com/charmbracelet/lipgloss) - Style definitions for beautiful terminal output
- [Bubbles](https://github.com/charmbracelet/bubbles) - TUI components for common interactions
- [Huh](https://github.com/charmbracelet/huh) - Forms and prompts for interactive input

### ⚡ Infrastructure
- [Solana](https://solana.com) - High-performance blockchain powering GhostSpeak
- [Cobra](https://github.com/spf13/cobra) - CLI framework for command structure
- [Viper](https://github.com/spf13/viper) - Configuration management
- [BadgerDB](https://github.com/dgraph-io/badger) - Fast embedded key-value storage

Special thanks to the [Charm](https://github.com/charmbracelet) team for creating the tools that make terminals beautiful!

## 💬 Support

- 📧 Email: support@ghostspeak.ai
- 💬 Discord: https://discord.gg/ghostspeak
- 🐦 Twitter: [@ghostspeak_ai](https://twitter.com/ghostspeak_ai)

---

**Built with 👻 by the GhostSpeak team**
