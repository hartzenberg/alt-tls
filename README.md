# alt-tls

**Legacy TLS 1.0/1.1 implementation** forked from Go 1.17.13 `crypto/tls`.

## 🎯 Purpose

Provides **full TLS 1.0/1.1 support** including all cipher suites for applications
that need compatibility with legacy clients (e.g., OpenVPN 2.2.x).

**Use Case**: OpenVPN servers that need to support both:
- Modern clients (TLS 1.2+, AEAD ciphers)
- Legacy clients (TLS 1.0, CBC ciphers, 3DES, RC4)

## 📦 Installation

```bash
go get github.com/hartzenberg/alt-tls
```

## 🚀 Usage

### Dual TLS Configuration (Legacy + Modern)

```go
package main

import (
    "crypto/tls"  // Standard library (TLS 1.2+ only in Go 1.24+)
    alttls "github.com/hartzenberg/alt-tls/tls"  // Full TLS 1.0 support
)

func main() {
    // Legacy listener with TLS 1.0 support
    legacyConfig := &alttls.Config{
        Certificates: certs,
        MinVersion:   alttls.VersionTLS10,  // ✅ TLS 1.0 available
        MaxVersion:   alttls.VersionTLS12,
        CipherSuites: []uint16{
            alttls.TLS_RSA_WITH_3DES_EDE_CBC_SHA,  // ✅ Available!
            alttls.TLS_RSA_WITH_AES_256_CBC_SHA,
            alttls.TLS_RSA_WITH_AES_128_CBC_SHA,
        },
    }
    
    // Modern listener with standard library TLS
    modernConfig := &tls.Config{
        Certificates: certs,
        MinVersion:   tls.VersionTLS12,  // TLS 1.2+ only
    }
    
    // Both can coexist in the same binary!
}
```

### Single TLS 1.0 Support

```go
import alttls "github.com/hartzenberg/alt-tls/tls"

config := &alttls.Config{
    Certificates: certs,
    MinVersion:   alttls.VersionTLS10,
    ClientAuth:   alttls.RequireAndVerifyClientCert,
}

listener, _ := alttls.Listen("tcp", ":1194", config)
```

## 📂 Package Structure

```
github.com/hartzenberg/alt-tls/
├── tls/          ← crypto/tls from Go 1.17.13
├── x509/         ← crypto/x509 from Go 1.17.13
└── internal/     ← Required internal packages
```

## 🔒 Security Notice

**TLS 1.0 and 1.1 have known vulnerabilities:**
- BEAST attack (CVE-2011-3389)
- Weak CBC cipher modes
- Deprecated by major browsers and standards bodies

**Use only for legacy compatibility** with appropriate mitigations:
- ✅ Isolate to specific network interface/port
- ✅ Use additional authentication layer (e.g., HMAC, IPsec)
- ✅ Monitor and log all TLS 1.0 connections
- ✅ Plan migration timeline to TLS 1.2+

## 🔄 Maintenance

This is a **frozen fork** for legacy compatibility purposes.

- **Source**: Go 1.17.13 (August 2022)
- **Updates**: Security patches may be backported manually
- **Support**: Community-driven, use at own risk
- **Future**: Will not track Go's crypto/tls development

For new projects, use Go's standard `crypto/tls` (TLS 1.2+).

## 🆚 Comparison with Standard Library

| Feature | alt-tls | crypto/tls (Go 1.24+) |
|---------|---------|----------------------|
| TLS 1.0 | ✅ Full support | ❌ Removed |
| TLS 1.1 | ✅ Full support | ❌ Removed |
| TLS 1.2 | ✅ Supported | ✅ Supported |
| TLS 1.3 | ✅ Supported | ✅ Supported |
| 3DES cipher | ✅ Available | ❌ Removed |
| RC4 cipher | ✅ Available | ❌ Removed |
| CBC ciphers | ✅ All available | ⚠️ Limited |

## 📜 License

**BSD-3-Clause** (same as Go standard library)

Copyright (c) 2009 The Go Authors. All rights reserved.

See LICENSE file for full text.

## 🙏 Credits

- **Go Team**: Original crypto/tls implementation
- **This Fork**: Maintained by [@hartzenberg](https://github.com/hartzenberg)

## 📚 Related

- [Go crypto/tls documentation](https://pkg.go.dev/crypto/tls)
- [TLS 1.0 RFC 2246](https://tools.ietf.org/html/rfc2246)
- [OpenVPN TLS requirements](https://openvpn.net/community-resources/)

## ⚠️ Disclaimer

This package is provided "as is" for legacy compatibility purposes. The maintainers
are not responsible for security issues arising from the use of deprecated TLS versions.

**Recommended**: Migrate to TLS 1.2+ as soon as feasible.
