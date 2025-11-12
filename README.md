# alt-tls

**Legacy TLS 1.0/1.1 implementation** forked from Go 1.17.13 `crypto/tls`.

## 🎯 Purpose

Provides **full TLS 1.0/1.1 support** including all cipher suites for applications
that need compatibility with legacy clients (e.g., OpenVPN 2.2.x).

## 📦 Installation

```bash
go get github.com/hartzenberg/alt-tls
```

## 🚀 Usage

```go
package main

import (
    alttls "github.com/hartzenberg/alt-tls/tls"
    altx509 "github.com/hartzenberg/alt-tls/x509"
)

func main() {
    config := &alttls.Config{
        Certificates: certs,
        MinVersion:   alttls.VersionTLS10,  // ✅ TLS 1.0 available
        CipherSuites: []uint16{
            alttls.TLS_RSA_WITH_3DES_EDE_CBC_SHA,  // ✅ Available!
            alttls.TLS_RSA_WITH_AES_256_CBC_SHA,
        },
    }
    // Use config...
}
```

## 📂 Package Structure

```
github.com/hartzenberg/alt-tls/
├── tls/          ← crypto/tls from Go 1.17.13
├── x509/         ← crypto/x509 from Go 1.17.13
│   └── pkix/     ← crypto/x509/pkix subpackage
└── internal/     ← Required internal packages
```

## 🔒 Security Notice

TLS 1.0/1.1 have known vulnerabilities. Use only for legacy compatibility.

## 📜 License

BSD-3-Clause (same as Go standard library)
