[![Cargo build](https://github.com/pepa65/domaincheck/actions/workflows/rust.yml/badge.svg)](https://github.com/pepa65/domaincheck/actions/workflows/rust.yml)
[![Dependencies](https://deps.rs/repo/github/pepa65/domaincheck/status.svg)](https://deps.rs/repo/github/pepa65/domaincheck)
[![Docs](https://img.shields.io/badge/Docs-domaincheck-blue)](https://docs.rs/crate/domaincheck/latest)
[![License](https://img.shields.io/badge/License-MIT-blue)](https://github.com/pepa65/domaincheck/blob/main/LICENSE)
[![Downloads](https://img.shields.io/crates/d/domaincheck.svg)](https://crates.io/crates/domaincheck)

# domaincheck v0.4.24
**Check domainname availability on CLI using RDAP (with WHOIS fallback)**

A fast, robust CLI tool for checking domain availability using RDAP protocol with automatic WHOIS fallback and detailed domain information.

## Features
* ✅ **RDAP Protocol Support** - Uses the modern Registration Data Access Protocol
* 🔄 **IANA Bootstrap Registry** - Dynamically discovers RDAP endpoints for any TLD
* 🌐 **Automatic WHOIS Fallback** - Gracefully falls back to WHOIS when RDAP isn't available
* 🔍 **Detailed Information** - Shows registrar, creation dates, expiration, and status
* 🎯 **Multiple TLD Support** - Check domains across various TLDs in one command
* 📋 **Bulk Domain Checking** - Process hundreds of domains from a text file
* 🚀 **Optimized Concurrency** - Lightning-fast parallel processing with intelligent rate limiting and TLD-based optimization
* 💻 **Interactive Terminal UI** - Navigate and explore domains in a beautiful terminal interface
* 📋 **JSON Output** - Machine-readable output for integration with other tools
* 🎨 **Color-coded Results** - Clear visual indicators for domain status
* 🐛 **Debug Mode** - Detailed logging for troubleshooting
* ⚡ **Lightweight & Fast**
* After: https://github.com/saidutt46/domain-check

## Install static single-binary
```
wget https://github.com/pepa65/domaincheck/releases/download/0.4.7/domaincheck
sudo mv domaincheck /usr/local/bin
sudo chown root:root /usr/local/bin/domaincheck
sudo chmod +x /usr/local/bin/domaincheck
```

## Install with cargo
If not installed yet, install a **Rust toolchain**, see https://www.rust-lang.org/tools/install

### Direct from crates.io
```
cargo install domaincheck
```

### Direct from repo
```
cargo install --git https://github.com/pepa65/domaincheck
```

### Static build (avoiding GLIBC incompatibilities)
```
git clone https://github.com/pepa65/domaincheck
cd domaincheck
rustup target add x86_64-unknown-linux-musl
cargo rel  # Alias defined in .cargo/config.toml
```
The binary will be at `target/x86_64-unknown-linux-musl/release/domaincheck`

## Install with cargo-binstall
Even without a full Rust toolchain, rust binaries can be installed with the static binary `cargo-binstall`:

```
# Install cargo-binstall for Linux x86_64
# (Other versions are available at https://crates.io/crates/cargo-binstall)
wget github.com/cargo-bins/cargo-binstall/releases/latest/download/cargo-binstall-x86_64-unknown-linux-musl.tgz
tar xf cargo-binstall-x86_64-unknown-linux-musl.tgz
sudo chown root:root cargo-binstall
sudo mv cargo-binstall /usr/local/bin/
```

Only a linux-x86_64 (musl) binary available: `cargo-binstall domaincheck`

It will be installed in `~/.cargo/bin/` which will need to be added to `PATH`!

## Quick Start
Check domainname availability (TLD defaults to .com):

`domaincheck example`

Check a domain across multiple TLDs:

`domaincheck example -t com org net io app`

Get more detailed information about a domain:

`domaincheck example.com -i`

Check multiple domains from a file:

`domaincheck --file domains.txt`

## Usage
```
domaincheck 0.4.24
Check domainname availability on CLI using RDAP (with WHOIS fallback)
Usage:
  domaincheck [OPTIONS] [DOMAIN]
Arguments:
  [DOMAIN]  Domainname to check (without TLD for multiple TLD checking)

Options:
  -t, --tld <TLD>...               Check availability with these TLDs (space separated)
  -f, --file <FILE>                Input file with domains to check (one per line)
  -j, --json                       Output results in JSON format
  -u, --ui                         Launch interactive terminal UI dashboard
  -c, --concurrency <CONCURRENCY>  Max concurrent domain checks (max: 100) [default: 10]
  -n, --no-whois                   Disable automatic WHOIS fallback
  -v, --verbose                    Be extra verbose
  -d, --debug                      Show detailed debug information and error messages
  -h, --help                       Print help
  -V, --version                    Print version
```

## Examples
### Basic domain check
```
domaincheck example
```

Output:
```
🔴 example.com TAKEN
```

### Check with multiple TLDs
```
domaincheck myawesome -t com net org io
```

Output:
```
🔴 myawesome.com TAKEN
🟢 myawesome.net AVAILABLE
🟢 myawesome.org AVAILABLE
🔴 myawesome.io TAKEN
```

### Show detailed domain information
```
domaincheck google.com -i -p
```

Output:
```
🔍 Checking: google with TLDs: com
🔴 google.com TAKEN  1997-09-15T04:00:00Z..2028-09-14T04:00:00Z @"MarkMonitor Inc." serverDeleteProhibited, serverTransferProhibited, serverUpdateProhibited
```

### Bulk domain checking from file
File domains.txt contains:
```
example.com
xyz123domain
startup.io
# This is a comment
test-site.org
```

```
domaincheck --file domains.txt
```

Output:
```
Starting bulk domain check with concurrency: 10
Results will stream as they complete:

🟢 xyz123domain.com AVAILABLE (No info available for unregistered domains)
🔴 example.com TAKEN  1995-08-14T04:00:00Z..2025-08-13T04:00:00Z @"RESERVED-Internet Assigned Numbers Authority" client delete prohibited, client transfer prohibited, client update prohibited
🔴 test-site.org TAKEN (No info available)
🔴 startup.io TAKEN (No info available)

✅ 4 domains processed: 🟢 1 available, 🔴 3 taken, ⚠️ 0 unknown
```

### Bulk checking with high concurrency
```
domaincheck --file many-domains.txt --concurrency 50
```

### Bulk checking with TLD specification
```
domaincheck --file base-domains.txt --tld com org io
```

### Interactive UI mode
```
domaincheck startup -t com io xyz dev -u
```

### Debug mode for troubleshooting
```
domaincheck example.pizza -d
```

Output:
```
🔍 Checking: example with TLDs: pizza
🔍 No known RDAP endpoint for .pizza, trying bootstrap registry...
🟢 example.pizza still AVAILABLE (No info available for unregistered domains)
```

### JSON output for integration
```
domaincheck example -j
```

Output:
```
[
  {
    "domain": "example.com",
    "available": false,
    "info": {
      "registrar": "RESERVED-Internet Assigned Numbers Authority",
      "creation_date": "1995-08-14T04:00:00Z",
      "expiration_date": "2025-08-13T04:00:00Z",
      "status": [
        "client delete prohibited",
        "client transfer prohibited",
        "client update prohibited"
      ]
    }
  }
]
```

### Piping results to other tools
```
domaincheck mydomain -t com net org -j |jq '.[] | select(.available==true) | .domain'
```

```
domaincheck example.com -ji |jq '.[] | .info.expiration_date'
```

## Integration
The JSON output can be easily integrated with other tools:
```
# Find all available domains and save to a file
domaincheck business -t com net org io xyz -j |jq '.[] | select(.available==true) | .domain' -r > available_domains.txt
```

```
# Find all available domains in a file and save to a file
domaincheck --file domains.txt -j | jq '.[] | select(.available==true) | .domain' -r > available_domains.txt
```

## How It Works
1. Attempts to check domain via RDAP using known registry endpoints
2. If TLD isn't in the known list, uses IANA bootstrap to discover the endpoint
3. Automatically falls back to WHOIS lookup if RDAP is unavailable or unsuccessful
4. Extracts detailed information when requested and possible
5. Uses intelligent concurrency control with rate limiting to prevent overloading servers

## Performance
* Concurrent processing of multiple domains (up to 5 at once by default)
* Rate limiting to prevent overloading RDAP endpoints
* Automatic timeout handling to prevent hanging requests
* Small binary size for fast startup and low resource usage

## Supported TLDs
`domaincheck` includes built-in support for many popular TLDs including:

`com`, `net`, `org`, `io`, `app`, `dev`, `ai`, `co`, `xyz`, `me`, `info`, `biz`, `us`, `uk`, `eu`, `tech`, `blog`, `page`, `zone`, `shop`, `de`, `ca`, `au`, `fr`, `es`, `it`, `nl`, `jp`, `tv`, `cc`, and others.

Additional TLDs can be checked using the bootstrap (`-b`) option.

## Comparison with other tools
| Feature | domain-check | whois-cli | dns-lookup |
|---------|--------------|-----------|------------|
| RDAP Protocol | ✅ | ❌ | ❌ |
| Bootstrap Registry | ✅ | ❌ | ❌ |
| Auto WHOIS Fallback | ✅ | ✅ | ❌ |
| Detailed Info | ✅ | ❌ | ❌ |
| Multiple TLDs | ✅ | ❌ | ✅ |
| Bulk File Checking | ✅ | ❌ | ❌ |
| Configurable Concurrency | ✅ | ❌ | ❌ |
| Interactive UI | ✅ | ❌ | ❌ |
| JSON Output | ✅ | ❌ | ✅ |
| Concurrency Control | ✅ | ❌ | ❌ |
| Speed | Fast ⚡ | Medium | Medium |

## Contributing
Contributions are welcome! Please feel free to submit a Pull Request.
1. Fork the repository.
2. Create your feature branch (`git checkout -b feature/amazing-feature`).
3. Commit your changes (`git commit -m 'Add some amazing feature'`).
4. Push to the branch (`git push origin feature/amazing-feature`).
5. Open a Pull Request.

## License
This project is licensed under either of:
* Apache License, Version 2.0, ([LICENSE-APACHE](LICENSE-APACHE) or http://www.apache.org/licenses/LICENSE-2.0)
* MIT license ([LICENSE-MIT](LICENSE-MIT) or http://opensource.org/licenses/MIT)

at your option.

## Acknowledgments
* [IANA](https://www.iana.org/) for providing the RDAP bootstrap registry.
* [Rustsec](https://rustsec.org/) for inspiration on the dual MIT/Apache licensing approach.
* The various registry operators for providing public RDAP endpoints.
