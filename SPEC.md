TASK DESCRIPTION:
Develop a comprehensive Go library that implements a local Monero Web Wallet by integrating moneroger for daemon management and go-monero-rpc-client for RPC operations, providing a secure WebUI interface via custom net.Listener implementations.

CONTEXT:
Core Technology Stack:
1. Backend Dependencies:
   - Daemon Management: https://github.com/opd-ai/moneroger
   - RPC Client: https://github.com/monero-ecosystem/go-monero-rpc-client
   - Go Standard Library (net/http, net)

2. Network Architecture:
   - Monero Daemon (monerod) endpoint
   - Wallet RPC (monero-wallet-rpc) endpoint
   - WebUI HTTP server

3. Security Considerations:
   - Password-protected access
   - Session management
   - No TLS implementation (explicitly excluded)

IMPLEMENTATION REQUIREMENTS:

1. Library Structure
```go
/pkg
  /monerowallet
    wallet.go        // Core wallet type and constructor
    config.go        // Configuration definitions
    server.go        // HTTP server implementation
    api.go          // API endpoint handlers
    websocket.go    // Real-time updates
    session.go      // Session management
    /static         // Frontend assets
```

2. Core Interfaces
```go
type WalletServer interface {
    http.Handler
    Serve(net.Listener) error      // Serves wallet UI on provided listener
    Start(context.Context) error   // Starts wallet services
    Shutdown(context.Context) error
    Status() WalletStatus
}

type Config struct {
    // Wallet Configuration
    WalletFile    string
    Password      string           // Wallet file password
    WebUIPassword string          // Web interface password
    
    // Network Configuration
    DaemonAddr     string         // monerod RPC address (e.g., "localhost:18081")
    WalletRPCAddr  string         // monero-wallet-rpc address (e.g., "localhost:18082")
    
    // Optional Settings
    TestNet       bool
    DataDir       string
    
    // RPC Authentication (if enabled)
    RPCUsername   string          // Optional: monero-wallet-rpc auth
    RPCPassword   string          // Optional: monero-wallet-rpc auth
}

type WalletStatus struct {
    DaemonConnected    bool
    WalletRPCConnected bool
    Height             uint64
    Balance            uint64
    UnlockedBalance    uint64
}
```

3. API Endpoints
- Authentication:
  POST /api/login
  POST /api/logout

- Wallet Operations:
  GET  /api/balance
  POST /api/transfer
  GET  /api/addresses
  POST /api/addresses/new
  GET  /api/history

- Real-time Updates:
  WS   /api/updates

4. Implementation Steps:

Phase 1: Core Setup (2-3 days)
1. Initialize project structure
2. Implement configuration handling
3. Setup daemon management using moneroger
4. Create wallet RPC client integration with both endpoints

Phase 2: API Layer (3-4 days)
1. Implement HTTP server with net.Listener support
2. Create API endpoint handlers
3. Add session management
4. Setup WebSocket support

Phase 3: Frontend Development (3-4 days)
1. Create responsive HTML/CSS layout
2. Implement JavaScript client library
3. Add real-time updates via WebSocket
4. Create user interface components

USAGE EXAMPLE:
```go
func main() {
    cfg := monerowallet.Config{
        WalletFile:     "/path/to/wallet",
        Password:       "wallet_password",
        WebUIPassword:  "ui_password",
        DaemonAddr:     "localhost:18081",
        WalletRPCAddr:  "localhost:18082",
        // Optional RPC authentication
        RPCUsername:    "rpc_user",
        RPCPassword:    "rpc_password",
    }
    
    wallet, err := monerowallet.New(cfg)
    if err != nil {
        log.Fatal(err)
    }
    
    ctx := context.Background()
    if err := wallet.Start(ctx); err != nil {
        log.Fatal(err)
    }
    defer wallet.Shutdown(ctx)
    
    // Create custom listener for WebUI
    listener, err := net.Listen("tcp", "localhost:8080")
    if err != nil {
        log.Fatal(err)
    }
    
    // Serve wallet UI on custom listener
    log.Fatal(wallet.Serve(listener))
}

// Alternative usage with http.Server
func alternativeUsage() {
    wallet, _ := monerowallet.New(cfg)
    listener, _ := net.Listen("tcp", "localhost:8080")
    
    server := &http.Server{
        Handler: wallet,
    }
    
    server.Serve(listener)
}
```

QUALITY REQUIREMENTS:

1. Connection Management:
- Maintain stable connections to both monerod and monero-wallet-rpc
- Handle connection failures gracefully
- Implement reconnection logic
- Monitor connection status

2. Security:
- Session-based authentication for WebUI
- Support for RPC authentication
- Password hashing
- Input validation
- Rate limiting

3. Documentation:
- GoDoc comments
- Usage examples
- API documentation
- Configuration guide

VERIFICATION CHECKLIST:
□ Implements http.Handler interface
□ Implements custom net.Listener support
□ Supports both daemon and wallet RPC connections
□ No TLS implementation included
□ No ListenAddr configuration
□ Password protection implemented
□ WebSocket support added
□ Proper error handling
□ Clean package structure
□ Example code provided
□ Documentation complete
