# fgpg

Bash script utility for managing GnuGPG keys across multiple systems.

## Features

- **Key Backup & Restore**: Backup GPG keys locally and restore them to remote systems
- **Cross-System Management**: Manage keys on both local and remote hosts via SSH
- **Default Key Management**: Easily set and update your default signing key
- **Key Cleanup**: Remove expired or revoked keys automatically
- **Key Listing**: View keys on local or remote systems with detailed subkey information
- **Key Scrubbing**: Safely remove keys from local or remote stores
- **Remote Version Checking**: Verify GPG versions on remote systems (GPG 2.4.x recommended for authentication keys)

## Prerequisites

- **GnuPG**: Must be installed on both local and remote systems
- **SSH**: Required for remote operations with passwordless SSH access configured
- **Bash** (3.2+) or **Zsh**: Compatible shell environments
- **curl**: For automatic downloading of include-common.sh if not present

## Installation

1. Clone or download the repository:
```bash
git clone https://github.com/franksplace/fgpg.git
cd fgpg
```

2. Make the script executable:
```bash
chmod +x fgpg
```

3. Optionally add to your PATH for system-wide access:
```bash
# Add to ~/.bashrc or ~/.zshrc
export PATH="$PATH:/path/to/fgpg"
```

## Usage

```bash
fgpg [global option] command [command option]
```

### Global Options

- `-d, --debug` : Enable debug logging
- `-v, --verbose` : Enable verbose logging
- `-s, --short` : Utilize short keyid format (default)
- `-l, --long` : Utilize long keyid format (full fingerprint)

### Commands

#### `list [host]`
List GPG keys on local or remote systems. When no host is specified, lists local keys.

```bash
# List local keys
fgpg list

# List keys on remote host
fgpg list user@remotehost.com
```

#### `backup <key>`
Backup a key and its ownership trust information to local files.

```bash
# Backup key by email or ID
fgpg backup user@example.com

# Creates two files:
# - user@example.com-backup-TIMESTAMP.gpg (key material)
# - user@example.com-otrust-TIMESTAMP.gpg (trust database)
```

#### `sync-backup <key file> <otrust file> <host>`
Import backup files to a remote key store. Validates connectivity, imports key material and trust database, and verifies successful import.

```bash
# Sync backup to remote host
fgpg sync-backup user@example.com-backup-20240101.gpg user@example.com-otrust-20240101.gpg user@remotehost.com
```

#### `import <key file>`
Import a key file into the local GPG store.

```bash
fgpg import backup-key.gpg
```

#### `import-otrust <otrust file>`
Import ownership trust information into the local trust database.

```bash
fgpg import-otrust trust-file.gpg
```

#### `update-default-key <key>`
Set the default key in GPG configuration.

```bash
# Set by email address or key ID
fgpg update-default-key user@example.com
fgpg update-default-key ABCD1234
```

#### `cleanup`
Remove expired or revoked keys from the GPG keyring.

```bash
fgpg cleanup
```

#### `scrub <key> [host]`
Remove a key from local or remote GPG stores. Deletes both secret and public keys using fingerprint verification.

```bash
# Scrub local key
fgpg scrub user@example.com

# Scrub key from remote host
fgpg scrub user@example.com user@remotehost.com
```

#### `remote-version <host> ...`
Check GPG versions on remote systems. Useful for verifying compatibility (GPG 2.4.x recommended for authentication keys).

```bash
fgpg remote-version user@remotehost.com
fgpg remote-version host1.com host2.com host3.com
```

## Common Workflows

### Backup a key and sync to remote system
```bash
# 1. Create backup
fgpg backup user@example.com

# 2. Upload files to remote host
scp user@example.com-*.gpg user@remotehost.com:~

# 3. Login to remote and import
fgpg sync-backup user@example.com-backup-*.gpg user@example.com-otrust-*.gpg localhost
```

### Manage keys across multiple systems
```bash
# Check remote GPG versions
fgpg remote-version host1.com host2.com host3.com

# List keys on all systems
fgpg list
fgpg list host1.com
fgpg list host2.com

# Backup primary key
fgpg backup user@example.com

# Sync to remote systems
fgpg sync-backup user@example.com-backup-*.gpg user@example.com-otrust-*.gpg host1.com
fgpg sync-backup user@example.com-backup-*.gpg user@example.com-otrust-*.gpg host2.com
```

### Clean up expired keys
```bash
# View expired keys before cleanup
fgpg list -v

# Remove expired keys
fgpg cleanup
```

## Configuration

The script automatically handles SSH paths for Homebrew installations on macOS systems. If you need to customize the remote PATH for GPG, modify the `REMOTE_HOST_PATH` variable in the script.

## Dependencies

The script uses `include-common.sh` for common utilities and logging functions. If not found locally, it will automatically download from:
https://github.com/franksplace/shell-scripts

## License

Apache License 2.0 - See [LICENSE](LICENSE) for details

## Author

Frank Stutz - https://github.com/franksplace
