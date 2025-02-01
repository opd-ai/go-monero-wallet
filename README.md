# go-monero-webwallet

[![Go Reference](https://pkg.go.dev/badge/github.com/yourusername/go-monero-webwallet.svg)](https://pkg.go.dev/github.com/yourusername/go-monero-webwallet)
[![Go Report Card](https://goreportcard.com/badge/github.com/yourusername/go-monero-webwallet)](https://goreportcard.com/report/github.com/yourusername/go-monero-webwallet)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

> ⚠️ **Work In Progress**: This library is under active development and not yet ready for production use.

A Go library for creating local Monero web wallets with a secure WebUI interface. It provides seamless integration between Monero daemon management and wallet RPC operations.

## Features

- 🔒 Local-only web interface for Monero wallet management
- 🚀 Integrated daemon and wallet RPC process management
- 💼 Complete wallet operations (balance, transfers, addresses)
- ⚡ Real-time updates via WebSocket
- 🔐 Session-based authentication
- 🛠️ Custom network listener support
- 📱 Responsive web interface

## Prerequisites

- Go 1.21 or later
- Monero binaries (`monerod` and `monero-wallet-rpc`) in system PATH
- Write permissions for data directory

## Installation

```bash
go get github.com/yourusername/go-monero-webwallet
```

## Quick Start

```go
package main

import (
    "context"
    "log"
    "net"
    
    webwallet "github.com/yourusername/go-monero-webwallet"
)

func main() {
    // Configure the wallet
    cfg := webwallet.Config{
        WalletFile:    "/path/to/wallet",
        Password:      "wallet_password",
        WebUIPassword: "ui_password",
        DaemonAddr:    "localhost:18081",
        WalletRPCAddr: "localhost:18082",
    }
    
    // Create new wallet instance
    wallet, err := webwallet.New(cfg)
    if err != nil {
        log.Fatal(err)
    }
    
    // Start wallet services
    ctx := context.Background()
    if err := wallet.Start(ctx); err != nil {
        log.Fatal(err)
    }
    defer wallet.Shutdown(ctx)
    
    // Create listener for web interface
    listener, err := net.Listen("tcp", "localhost:8080")
    if err != nil {
        log.Fatal(err)
    }
    
    // Serve the wallet UI
    log.Printf("Wallet UI available at http://localhost:8080")
    log.Fatal(wallet.Serve(listener))
}
```

## Configuration

```go
type Config struct {
    // Wallet Configuration
    WalletFile    string // Path to .keys file
    Password      string // Wallet password
    WebUIPassword string // Web interface password
    
    // Network Configuration
    DaemonAddr    string // monerod RPC address
    WalletRPCAddr string // monero-wallet-rpc address
    
    // Optional Settings
    TestNet bool   // Use testnet instead of mainnet
    DataDir string // Custom data directory
    
    // RPC Authentication (optional)
    RPCUsername string
    RPCPassword string
}
```

## Security Considerations

- The web interface is designed for local use only
- No TLS support is implemented (use reverse proxy if needed)
- Session-based authentication is enforced
- All wallet operations require authentication
- Input validation and rate limiting are implemented

## Project Status

Current development status and upcoming features:

- [x] Core wallet management
- [x] Configuration handling
- [x] Process management
- [x] HTTP server implementation
- [ ] WebSocket updates
- [ ] Complete API endpoints
- [ ] Frontend implementation
- [ ] Session management
- [ ] Documentation
- [ ] Tests

## Contributing

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## Development Requirements

- Go 1.21+
- golangci-lint
- Monero binaries for testing
- Node.js and npm for frontend development

## License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## Acknowledgments

- [moneroger](https://github.com/opd-ai/moneroger) for daemon management
- [go-monero-rpc-client](https://github.com/monero-ecosystem/go-monero-rpc-client) for RPC operations
- Monero Project for the core Monero software

## Disclaimer

This software is in active development and should be used with caution. Always backup your wallet files and test thoroughly before using with real funds.
