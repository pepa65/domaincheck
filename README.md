# domain-check

![Crates.io Version](https://img.shields.io/crates/v/domain-check)
![Crates.io License](https://img.shields.io/crates/l/domain-check)
![Crates.io Downloads](https://img.shields.io/crates/d/domain-check)

A fast, robust CLI tool for checking domain availability using RDAP protocol with automatic WHOIS fallback and detailed domain information.

## Features

- ✅ **RDAP Protocol Support** - Uses the modern Registration Data Access Protocol
- 🔄 **IANA Bootstrap Registry** - Dynamically discovers RDAP endpoints for any TLD
- 🌐 **Automatic WHOIS Fallback** - Gracefully falls back to WHOIS when RDAP isn't available
- 🔍 **Detailed Information** - Shows registrar, creation dates, expiration, and status
- 🎯 **Multiple TLD Support** - Check domains across various TLDs in one command
- 📋 **Bulk Domain Checking** - Process hundreds of domains from a text file
- 💻 **Interactive Terminal UI** - Navigate and explore domains in a beautiful terminal interface
- 🚀 **Optimized Concurrency** - Fast parallel processing with rate limiting and error handling
- 📋 **JSON Output** - Machine-readable output for integration with other tools
- 🎨 **Color-coded Results** - Clear visual indicators for domain status
- 🐛 **Debug Mode** - Detailed logging for troubleshooting
- ⚡ **Lightweight & Fast**

## Installation

### From crates.io

```bash
cargo install domain-check
```

### From source

```bash
git clone https://github.com/saidutt46/domain-check.git
cd domain-check
cargo install --path .
```

## Quick Start

Check if a domain is available:

```bash
domain-check example
```

Check a domain across multiple TLDs:

```bash
domain-check example -t com org net io app
```

Get detailed information about a domain:

```bash
domain-check example.com -i
```

Check multiple domains from a file:

```bash
domain-check --file domains.txt
```

## Usage

```
USAGE:
  domain-check [OPTIONS] [DOMAIN]

ARGS:
  [DOMAIN]  Domain name to check (without TLD for multiple TLD checking)

OPTIONS:
  -t, --tld <TLD>...       Check availability with these TLDs
  -f, --file <FILE>        Input file with domains to check (one per line)
  -c, --concurrency <N>    Max concurrent domain checks (default: 10, max: 100)
      --force              Override the 500 domain limit for bulk operations
  -i, --info               Show detailed domain information when available
  -b, --bootstrap          Use IANA bootstrap to find RDAP endpoints for unknown TLDs
  -w, --whois              Fallback to WHOIS when RDAP is unavailable (deprecated, enabled by default)
      --no-whois           Disable automatic WHOIS fallback
  -u, --ui                 Launch interactive terminal UI dashboard
  -j, --json               Output results in JSON format
  -p, --pretty             Enable colorful, formatted output
  -d, --debug              Show detailed debug information and error messages
  -h, --help               Print help information
  -V, --version            Print version information
```

## Examples

### Basic domain check

```bash
domain-check example
```

Output:
```
🔴 example.com is TAKEN
```

### Check with multiple TLDs

```bash
domain-check myawesome -t com net org io
```

Output:
```
🔴 myawesome.com is TAKEN
🟢 myawesome.net is AVAILABLE
🟢 myawesome.org is AVAILABLE
🔴 myawesome.io is TAKEN
```

### Show detailed domain information

```bash
domain-check google.com -i -p
```

Output:
```
🔍 Checking domain availability for: google
🔍 With TLDs: com
ℹ️ Detailed info will be shown for taken domains

🔴 google.com is TAKEN Registrar: MarkMonitor Inc. | Created: 1997-09-15T04:00:00Z | Expires: 2028-09-14T04:00:00Z | Status: serverDeleteProhibited, serverTransferProhibited, serverUpdateProhibited
```

### Bulk domain checking from file

```bash
domain-check --file domains.txt
```

Where domains.txt contains:
```
example.com
mydomain
startup.io
# This is a comment
test-site.org
```

Output:
```
Checking 4 domains from file...
Using concurrency: 10 - Please wait...

🔴 example.com is TAKEN
🟢 mydomain.com is AVAILABLE
🔴 startup.io is TAKEN
🟢 test-site.org is AVAILABLE

✅ 4 domains processed: 🟢 2 available, 🔴 2 taken, ⚠️ 0 unknown
```

### Bulk checking with high concurrency

```bash
domain-check --file many-domains.txt --concurrency 50
```

### Bulk checking with TLD specification

```bash
domain-check --file base-domains.txt --tld com org io
```

### Interactive UI mode

```bash
domain-check startup -t com io xyz dev -u
```

### Checking unknown TLDs with bootstrap

```bash
domain-check example.pizza -b
```

Output:
```
🔴 example.pizza is TAKEN
```

### Debug mode for troubleshooting

```bash
domain-check example.pizza -b -d
```

Output:
```
🔍 No known RDAP endpoint for .pizza, trying bootstrap registry...
🔍 Finding RDAP endpoint for TLD: pizza
🔍 Found endpoint: https://rdap.donuts.co/domain/
🔴 example.pizza is TAKEN
```

### JSON output for integration

```bash
domain-check example -j
```

## Advanced Usage

### Bulk checking with domain files

Create a text file with one domain per line:
```
mydomain.com
product-name
startup.io
brand-new-idea
# Comment lines are ignored
my-app.dev
```

Then check all domains in a single command:
```bash
domain-check --file domains.txt --concurrency 20
```

For base domains without TLDs, you can specify which TLDs to check:
```bash
domain-check --file base-domains.txt --tld com net org io app
```

### Checking available TLDs for a base name

```bash
domain-check mybusiness -t com net org io app dev xyz me co
```

### Checking rare TLDs with automatic fallback

```bash
domain-check rare-tld.something -b
```

The tool will automatically try RDAP first, then fall back to WHOIS if RDAP fails.

### Piping results to other tools

```bash
domain-check mydomain -t com net org -j | jq '.[] | select(.available==true) | .domain'
```

## Integration

The JSON output can be easily integrated with other tools:

```bash
# Find all available domains and save to a file
domain-check business -t com net org io xyz -j | jq '.[] | select(.available==true) | .domain' -r > available_domains.txt
```

```bash
# Find all available domains and save to a file
domain-check --file domains.txt -j | jq '.[] | select(.available==true) | .domain' -r > available_domains.txt
```

## How It Works

1. Attempts to check domain via RDAP using known registry endpoints
2. If TLD isn't in the known list, uses IANA bootstrap to discover the endpoint
3. Automatically falls back to WHOIS lookup if RDAP is unavailable or unsuccessful
4. Extracts detailed information when possible and requested
5. Uses intelligent concurrency control with rate limiting to prevent overloading servers

## Performance

- Concurrent processing of multiple domains (up to 5 at once by default)
- Rate limiting to prevent overloading RDAP endpoints
- Automatic timeout handling to prevent hanging requests
- Tiny binary size for fast startup and low resource usage

## Supported TLDs

domain-check includes built-in support for many popular TLDs including:

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

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add some amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## License

This project is licensed under either of:

- Apache License, Version 2.0, ([LICENSE-APACHE](LICENSE-APACHE) or http://www.apache.org/licenses/LICENSE-2.0)
- MIT license ([LICENSE-MIT](LICENSE-MIT) or http://opensource.org/licenses/MIT)

at your option.

## Acknowledgments

- [IANA](https://www.iana.org/) for providing the RDAP bootstrap registry
- [Rustsec](https://rustsec.org/) for inspiration on the dual MIT/Apache licensing approach
- Various registry operators for providing public RDAP endpoints