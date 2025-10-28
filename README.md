# FooToken - Fungible Token Example

**A complete, production-ready example implementation of a fungible token on Flow using Cadence 1.0.**

FooToken is an educational reference implementation demonstrating best practices for creating fungible tokens (FT) on Flow. This project provides a fully functional token contract with minting capabilities, transfers, metadata views, and account setup - perfect for developers learning Flow development or building their own custom tokens.

[![Flow Blockchain](https://img.shields.io/badge/Flow-Blockchain-00EF8B?style=flat&logo=flow&logoColor=white)](https://flow.com)
[![Cadence 1.0](https://img.shields.io/badge/Cadence-1.0-00D4FF?style=flat)](https://cadence-lang.org)
[![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](LICENSE)

## What It Does

FooToken implements the [Flow Fungible Token Standard], providing:

- **Token Contract**: A complete fungible token smart contract ([FooToken.cdc]) with vault management, supply tracking, and metadata support
- **Minting System**: Administrative minting capabilities for creating new tokens
- **Transfer Mechanism**: Safe token transfers between Flow accounts using resource-oriented programming
- **Account Setup**: Transaction for initializing accounts to receive FooTokens
- **Query Scripts**: Read-only scripts for checking balances and total supply
- **Metadata Integration**: Full support for MetadataViews enabling ecosystem discoverability
- **Cadence 1.0 Compliance**: Updated to the latest Flow blockchain standards with modern access control and capability patterns

**Token Details:**

- **Name**: Example Foo Token
- **Symbol**: EFT
- **Initial Supply**: 1,000 tokens (created at deployment)
- **Decimals**: UFix64 (8 decimal places of precision)
- **Standard**: Flow Fungible Token Standard (FT 2.0)

## Who It's For

### Beginner Developers

If you're new to Flow blockchain development, FooToken provides a clean, well-documented example of:
- Cadence smart contract structure
- Resource-oriented programming concepts
- Flow token standards implementation
- Transaction and script patterns

### Intermediate/Advanced Developers
Use FooToken as a template for:
- Building custom fungible tokens on Flow
- Understanding Cadence 1.0 migration patterns
- Implementing metadata views for token discoverability
- Learning Flow CLI configuration and deployment workflows

**Prerequisites:** Basic understanding of blockchain concepts and programming. No prior Cadence experience required.

## Technology Stack

- **Smart Contract Language**: [Cadence] - Flow's resource-oriented programming language
- **Blockchain Platform**: [Flow] - Fast, secure, and developer-friendly blockchain
- **Token Standard**: [FungibleToken] - Flow's fungible token standard interface
- **Metadata Standard**: [MetadataViews] - Standard for token metadata and discoverability
- **Development Tool**: [Flow CLI] - Command-line interface for Flow development
- **Network Support**: Emulator (local), Testnet, Mainnet

## Repository Structure

```
FooToken/
├── cadence/
│   ├── contracts/
│   │   └── FooToken.cdc              # Main token smart contract
│   ├── transactions/
│   │   ├── setup_ft_account.cdc      # Initialize account for FooToken
│   │   ├── mint_footoken.cdc         # Mint new tokens (admin only)
│   │   └── transfer_footoken.cdc     # Transfer tokens between accounts
│   └── scripts/
│       ├── get_footoken_balance.cdc  # Query account balance
│       └── get_total_supply.cdc      # Query total token supply
├── flow.json                          # Flow CLI configuration
└── README.md                          # This file
```

## Installation & Setup

### Step 1: Install Flow CLI

**macOS/Linux:**

```bash
sh -ci "$(curl -fsSL https://raw.githubusercontent.com/onflow/flow-cli/master/install.sh)"
```

**Windows (PowerShell):**

```powershell
iex "& { $(irm 'https://raw.githubusercontent.com/onflow/flow-cli/master/install.ps1') }"
```

**Verify installation:**

```bash
flow version
```

### Step 2: Clone the Repository

```bash
git clone https://github.com/onflow/FooToken.git
cd FooToken
```

### Step 3: Start Flow Emulator

The Flow emulator runs a local blockchain for development and testing:

```bash
flow emulator start
```

Keep this terminal running. Open a new terminal for the next steps.

### Step 4: Deploy the Contract

In a new terminal, deploy FooToken to the emulator:

```bash
flow project deploy --network emulator
```

You should see output confirming the deployment:

```
Deploying 1 contracts for accounts: emulator-account

FooToken -> 0xf8d6e0586b0a20c7
```

### Step 5: Verify Deployment

Check the initial total supply:

```bash
flow scripts execute cadence/scripts/get_total_supply.cdc --network emulator
```

Expected output: `1000.00000000` (the initial supply created in the contract)

## Usage Examples

### Example 1: Set Up an Account to Receive FooTokens

Before an account can receive FooTokens, it must initialize a vault in storage:

```bash
# Set up the emulator account (already has tokens from deployment)
flow transactions send cadence/transactions/setup_ft_account.cdc \
  --signer emulator-account \
  --network emulator
```

**What this does:**

- Creates an empty FooToken vault in the account's storage
- Publishes a public capability for receiving tokens
- Idempotent operation (safe to run multiple times)

### Example 2: Check Account Balance

Query the FooToken balance of any account:

```bash
# Check balance of emulator account (should show 1000.0)
flow scripts execute cadence/scripts/get_footoken_balance.cdc 0xf8d6e0586b0a20c7 \
  --network emulator
```

**Replace `0xf8d6e0586b0a20c7` with any account address.**

### Example 3: Transfer Tokens Between Accounts

Transfer FooTokens from one account to another:

```bash
# Transfer 100 tokens to another account
flow transactions send cadence/transactions/transfer_footoken.cdc \
  0x01cf0e2f2f715450 100.0 \
  --signer emulator-account \
  --network emulator
```

**Parameters:**

- `0x01cf0e2f2f715450` - Recipient account address (must be set up first)
- `100.0` - Amount to transfer

**Note:** Both accounts must have run `setup_ft_account.cdc` first.

### Example 4: Mint New Tokens (Admin Only)

Create new tokens and add them to circulation:

```bash
# Mint 500 new tokens to an account
flow transactions send cadence/transactions/mint_footoken.cdc \
  0xf8d6e0586b0a20c7 500.0 \
  --signer emulator-account \
  --network emulator
```

**Parameters:**

- `0xf8d6e0586b0a20c7` - Recipient account address
- `500.0` - Amount to mint

**Security:** Only the account that deployed the contract (holds the Minter resource) can mint tokens.

### Example 5: Query Total Supply

Check the total supply of all FooTokens in circulation:

```bash
flow scripts execute cadence/scripts/get_total_supply.cdc --network emulator
```

**Returns:** The sum of all tokens ever minted (increases with minting, decreases with burning)

## Working with Testnet/Mainnet

### Deploy to Testnet

1. **Create a testnet account** at [Flow Testnet Faucet]

2. **Update flow.json** with your testnet account:

```json
"accounts": {
  "testnet-account": {
    "address": "YOUR_TESTNET_ADDRESS",
    "key": "YOUR_PRIVATE_KEY"
  }
}
```

3. **Add testnet deployment configuration:**

```json
"deployments": {
  "testnet": {
    "testnet-account": ["FooToken"]
  }
}
```

4. **Deploy to testnet:**

```bash
flow project deploy --network testnet
```

### Production Considerations for Mainnet

Before deploying to mainnet:

- **Update Metadata**: Change token name, symbol, description in [FooToken.cdc:101-111]
- **Change Logo URL**: Replace the placeholder logo at [FooToken.cdc:96]
- **Security Audit**: Have your contract professionally audited
- **Test Thoroughly**: Run extensive tests on testnet first
- **Token Economics**: Plan your minting strategy and total supply carefully
- **Access Control**: Consider who should hold the Minter resource

## Key Concepts & Architecture

### Resource-Oriented Programming

FooToken uses Cadence's resource-oriented paradigm for security:

```cadence
// Vaults are resources - they cannot be copied or lost
access(all) resource Vault: FungibleToken.Vault {
    access(all) var balance: UFix64

    // Withdraw requires special capability
    access(FungibleToken.Withdraw) fun withdraw(amount: UFix64): @FooToken.Vault {
        self.balance = self.balance - amount
        return <-create Vault(balance: amount)
    }
}
```

**Key Resource Properties:**

- **Linear Types**: Resources cannot be copied, only moved (`<-` operator)
- **Guaranteed Destruction**: Must be explicitly destroyed or stored
- **Access Control**: Capabilities control who can perform operations

### Capability-Based Security

Access to token operations is controlled through capabilities:

```cadence
// Public capability - anyone can deposit
let receiverRef = recipient.capabilities.borrow<&{FungibleToken.Receiver}>(FooToken.VaultPublicPath)

// Private capability - only owner can withdraw
let vaultRef = signer.storage.borrow<auth(FungibleToken.Withdraw) &FooToken.Vault>(from: FooToken.VaultStoragePath)
```

**Capability Types:**

- **Public Receiver**: Anyone can send you tokens
- **Private Withdraw**: Only you can withdraw your tokens
- **Admin Minter**: Only deployer can mint new tokens

### Storage Paths

FooToken uses standardized storage paths:

- **VaultStoragePath** (`/storage/fooTokenVault`) - Private storage for your vault
- **VaultPublicPath** (`/public/fooTokenVault`) - Public capability for receiving
- **MinterStoragePath** (`/storage/fooTokenMinter`) - Admin minting capability

### Metadata Views

FooToken implements metadata standards for ecosystem integration:

```cadence
case Type<FungibleTokenMetadataViews.FTDisplay>():
    return FungibleTokenMetadataViews.FTDisplay(
        name: "Example Foo Token",
        symbol: "EFT",
        description: "This fungible token is used as an example...",
        externalURL: MetadataViews.ExternalURL("https://..."),
        logos: medias,
        socials: { "twitter": MetadataViews.ExternalURL("...") }
    )
```

This enables:

- Wallets to display token information
- Block explorers to show token metadata
- DEXes to list your token automatically
- Analytics platforms to track your token

## Common Commands Reference

```bash
# Start local development blockchain
flow emulator start

# Deploy contracts to emulator
flow project deploy --network emulator

# Run a read-only script
flow scripts execute <script-path> [args...] --network emulator

# Send a transaction
flow transactions send <transaction-path> [args...] --signer <account> --network emulator

# Create a new Flow account
flow accounts create

# Get account information
flow accounts get <address> --network emulator

# Check contract deployment status
flow accounts get 0xf8d6e0586b0a20c7 --network emulator
```

## Learning Resources & Documentation

### Official Flow Documentation

- [Creating a Fungible Token Guide]
- [Cadence Language Reference]
- [Flow Developer Portal]
- [Fungible Token Standard]
- [Cadence 1.0 Migration Guide]

### Tutorials
- [Fungible Tokens Tutorial]
- [Flow CLI Quick Reference]
- [Cadence Tutorial Series]

### Community & Support
- [Flow Discord]
- [Flow Forum]
- **Stack Overflow**: Tag questions with `flow-blockchain` and `cadence`
- [Flow Developer Twitter]

## Troubleshooting

### Error: "Could not borrow reference to the owner's Vault"
**Solution:** Run `setup_ft_account.cdc` for the account first.

### Error: "Cannot borrow Minter reference"
**Solution:** Only the contract deployer account can mint tokens. Use `--signer emulator-account`.

### Error: "Could not borrow receiver reference"
**Solution:** The recipient account needs to run `setup_ft_account.cdc` before receiving tokens.

### Emulator Won't Start
**Solution:** Check if port 3569 is already in use: `lsof -i :3569` and kill the process or use a different port.

### Contract Deployment Fails
**Solution:** Ensure the emulator is running and contracts are listed in the correct order in flow.json (dependencies first).


## Version History

- **v2.0** (2024) - Updated to Cadence 1.0 specification
  - Modern access control (`access(all)` instead of `pub`)
  - Entitlement-based capabilities
  - Updated capability APIs
  - MetadataViews integration

- **v1.0** (2020-2023) - Original Cadence pre-1.0 version
  - Basic fungible token implementation
  - Legacy capability syntax

## License

```
Copyright 2024 Flow Foundation

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```

<!-- Reference Links -->

[Flow Fungible Token Standard]: https://github.com/onflow/flow-ft
[FooToken.cdc]: cadence/contracts/FooToken.cdc
[Cadence]: https://cadence-lang.org
[Flow]: https://flow.com
[FungibleToken]: https://github.com/onflow/flow-ft
[MetadataViews]: https://github.com/onflow/flow-nft/blob/master/contracts/MetadataViews.cdc
[Flow CLI]: https://developers.flow.com/tools/flow-cli
[Flow Testnet Faucet]: https://testnet-faucet.onflow.org
[FooToken.cdc:101-111]: cadence/contracts/FooToken.cdc#L101-L111
[FooToken.cdc:96]: cadence/contracts/FooToken.cdc#L96
[Creating a Fungible Token Guide]: https://developers.flow.com/build/guides/fungible-token
[Cadence Language Reference]: https://cadence-lang.org
[Flow Developer Portal]: https://developers.flow.com
[Fungible Token Standard]: https://github.com/onflow/flow-ft
[Cadence 1.0 Migration Guide]: https://cadence-lang.org/docs/cadence-migration-guide/ft-guide
[Fungible Tokens Tutorial]: https://developers.flow.com/cadence/tutorial/06-fungible-tokens
[Flow CLI Quick Reference]: https://developers.flow.com/tools/flow-cli/quick-reference
[Cadence Tutorial Series]: https://developers.flow.com/cadence/tutorial/01-first-steps
[Flow Discord]: https://discord.gg/flow
[Flow Forum]: https://forum.flow.com
[Flow Developer Twitter]: https://twitter.com/flow_blockchain