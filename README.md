# ConvertCLI - NTFS to Linux Filesystem Converter

A powerful, interactive TUI-based bash tool for safely converting NTFS partitions to native Linux filesystems while preserving all your data.

![License](https://img.shields.io/badge/license-GPL%20v3-blue.svg)
![Platform](https://img.shields.io/badge/platform-Linux-lightgrey.svg)
![Shell](https://img.shields.io/badge/shell-bash-green.svg)
![Version](https://img.shields.io/badge/version-1.0.0-orange.svg)

## Overview

ConvertCLI provides a safe, iterative approach to converting NTFS partitions to Linux-native filesystems without data loss. Unlike traditional methods that require backing up data to external storage, this tool performs an in-place conversion by iteratively shrinking the NTFS partition and migrating files to the new filesystem.

### Why Use ConvertCLI?

- **No External Storage Required** - Convert in-place without needing backup drives
- **Data Preservation** - Iterative process ensures your files are safely migrated
- **Resume Support** - Interrupted? Pick up right where you left off
- **User-Friendly** - Beautiful TUI guides you through every step

---

## Features

### Core Functionality

| Feature | Description |
|---------|-------------|
| **In-Place Conversion** | Convert NTFS to Linux filesystems without external backup storage |
| **Iterative Migration** | Safely shrinks NTFS and migrates files in cycles until complete |
| **Resume Capability** | Automatic state saving allows resumption after interruption |
| **Progress Tracking** | Real-time progress bars and status updates |

### Supported Filesystems

| Filesystem | Best For | Key Features |
|------------|----------|--------------|
| **ext4** | General use | Standard Linux filesystem, excellent compatibility and stability |
| **btrfs** | Power users | Snapshots, compression, checksums, copy-on-write |
| **xfs** | Large files | High-performance, excellent for media and databases |
| **f2fs** | SSDs | Flash-optimized, superior SSD performance |
| **reiserfs** | Legacy systems | Efficient with small files (limited modern support) |
| **jfs** | Reliability | IBM journaling filesystem (limited resize support) |

### User Interface

- **Modern TUI Design** - Clean interface with Unicode box-drawing characters
- **Color Coded Output** - Visual feedback with ANSI colors (with ASCII fallback)
- **Multiple Navigation Options** - Arrow keys, vim-style (j/k), Enter, ESC
- **Responsive Layout** - Adapts to terminal size (minimum 80x24)

### Safety Features

- **Dry-Run Mode** - Preview all operations without making changes
- **State Persistence** - Automatic checkpoint saves after each operation
- **Signal Handling** - Graceful handling of Ctrl+C and termination signals
- **Verification Steps** - Integrity checks before critical operations
- **SSD Protection** - Automatically skips defragmentation on solid-state drives

### Smart Automation

- **Auto Dependency Installation** - Automatically installs required packages via pacman
- **HDD/SSD Detection** - Detects storage type for optimal operation settings
- **Existing Partition Detection** - Can utilize existing target filesystem partitions
- **Defragmentation Support** - Optional pre/post conversion defrag for HDDs

---

## Requirements

### System Requirements

- **Operating System**: Arch Linux (or compatible distribution with pacman)
- **Privileges**: Root/sudo access required
- **Terminal**: ANSI color support recommended (80x24 minimum)
- **Shell**: Bash 4.0+

### Dependencies

The following packages are required and will be automatically installed if missing:

| Package | Purpose |
|---------|---------|
| `ntfs-3g` | NTFS read/write support |
| `parted` | Partition manipulation |
| `rsync` | File synchronization |
| `util-linux` | Core utilities |
| Filesystem tools | Varies by target filesystem (e.g., `e2fsprogs`, `btrfs-progs`) |

---

## Installation

### Quick Start

```bash
# Clone the repository
git clone https://github.com/yourusername/ConvertCLI.git
cd ConvertCLI

# Make executable
chmod +x convert_ntfs_to_linux_fs.sh

# Run with sudo
sudo ./convert_ntfs_to_linux_fs.sh
```

### Manual Download

```bash
# Download the script directly
curl -O https://raw.githubusercontent.com/yourusername/ConvertCLI/main/convert_ntfs_to_linux_fs.sh
chmod +x convert_ntfs_to_linux_fs.sh
sudo ./convert_ntfs_to_linux_fs.sh
```

---

## Usage

### Basic Usage

```bash
sudo ./convert_ntfs_to_linux_fs.sh
```

The interactive TUI will guide you through:
1. Selecting the target disk
2. Choosing the destination filesystem
3. Confirming the conversion
4. Monitoring progress

### Command Line Options

| Option | Description |
|--------|-------------|
| `--help`, `-h` | Display help information |
| `--dry-run` | Preview operations without making changes |
| `--dummy-mode` | Test mode with simulated disk operations |

### Examples

```bash
# Standard interactive conversion
sudo ./convert_ntfs_to_linux_fs.sh

# Test what would happen without making changes
sudo ./convert_ntfs_to_linux_fs.sh --dry-run

# Display help
./convert_ntfs_to_linux_fs.sh --help
```

### Keyboard Navigation

| Key | Action |
|-----|--------|
| `↑` / `k` | Move selection up |
| `↓` / `j` | Move selection down |
| `Enter` | Confirm selection |
| `ESC` | Cancel / Go back |
| `q` | Quit application |

---

## How It Works

### Conversion Process

```
┌─────────────────────────────────────────────────────────────────┐
│                     NTFS to Linux Conversion                    │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│   Initial State:                                                │
│   ┌────────────────────────────────────────────────────────┐   │
│   │                    NTFS Partition                       │   │
│   │               [██████████████████████]                  │   │
│   └────────────────────────────────────────────────────────┘   │
│                                                                 │
│   Iteration 1: Shrink NTFS, Create Target, Migrate Files        │
│   ┌──────────────────────────┬─────────────────────────────┐   │
│   │     NTFS (smaller)       │      Target Filesystem      │   │
│   │     [████████████]       │      [████████]             │   │
│   └──────────────────────────┴─────────────────────────────┘   │
│                                                                 │
│   Iteration N: Continue until NTFS is empty                     │
│   ┌──────┬─────────────────────────────────────────────────┐   │
│   │ NTFS │              Target Filesystem                  │   │
│   │ [██] │              [████████████████████]             │   │
│   └──────┴─────────────────────────────────────────────────┘   │
│                                                                 │
│   Final State: Delete NTFS, Expand Target                       │
│   ┌────────────────────────────────────────────────────────┐   │
│   │              Target Filesystem (Full Disk)             │   │
│   │               [██████████████████████]                 │   │
│   └────────────────────────────────────────────────────────┘   │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

### Step-by-Step Process

1. **Detection** - Scans selected disk for NTFS partitions
2. **Selection** - Choose target filesystem from interactive menu
3. **Analysis** - Calculates used space and estimates iterations needed
4. **Iterative Conversion**:
   - Shrink NTFS partition to used space + safety buffer
   - Create/expand target filesystem in freed space
   - Migrate files from NTFS to target using rsync
   - Repeat until NTFS is empty
5. **Finalization** - Delete empty NTFS partition and expand target to fill disk
6. **Optional Defrag** - Offer defragmentation for HDDs

---

## State Management

### Automatic State Saving

ConvertCLI automatically saves progress after each major operation to:

```
~/.ntfs_to_linux_fs/state_<device>.conf
```

### State Information Tracked

- Selected disk and partitions
- Target filesystem type
- Current iteration number
- Last completed operation
- Files migrated count

### Resuming an Interrupted Conversion

Simply run the script again - it will automatically detect the saved state and offer to resume:

```bash
sudo ./convert_ntfs_to_linux_fs.sh
# Script detects existing state and prompts:
# "Previous conversion state found. Resume? [Yes/No]"
```

---

## Filesystem-Specific Notes

### ext4

- **Resize Method**: Device-based (no mount required)
- **Best For**: General purpose, maximum compatibility
- **Notes**: Standard choice for most Linux installations

### btrfs

- **Resize Method**: Requires mounted filesystem
- **Best For**: Advanced users wanting snapshots, compression
- **Notes**: Supports online resize, consider enabling compression

### xfs

- **Resize Method**: Requires mounted filesystem
- **Best For**: Large files, high-performance workloads
- **Notes**: Can only grow (cannot shrink), excellent for media servers

### f2fs

- **Resize Method**: Device-based
- **Best For**: SSDs and flash storage
- **Notes**: Optimized for flash translation layers, best SSD performance

### reiserfs

- **Resize Method**: Device-based
- **Best For**: Legacy systems with many small files
- **Notes**: Limited modern support, consider alternatives

### jfs

- **Resize Method**: Device-based
- **Best For**: Reliability-focused systems
- **Notes**: Limited resize capabilities

---

## Troubleshooting

### Common Issues

#### "No disks found"

```bash
# Check for available block devices
lsblk

# Ensure you're running as root
sudo ./convert_ntfs_to_linux_fs.sh
```

#### Package installation fails

```bash
# Update package database
sudo pacman -Sy

# Check network connectivity
ping -c 3 archlinux.org

# Manual installation if needed
sudo pacman -S ntfs-3g parted rsync util-linux
```

#### Conversion interrupted

1. Check state file: `~/.ntfs_to_linux_fs/`
2. Run script again to resume
3. Verify disk space availability
4. Check filesystem integrity:
   ```bash
   # For NTFS
   sudo ntfsfix /dev/sdXY
   
   # For ext4
   sudo e2fsck -f /dev/sdXY
   ```

#### Terminal display issues

```bash
# Check terminal type
echo $TERM

# Ensure UTF-8 locale
export LANG=en_US.UTF-8

# Try with minimum terminal size
# Resize terminal to at least 80x24
```

#### "Partition is mounted"

```bash
# Unmount the partition first
sudo umount /dev/sdXY

# Check what's using it
lsof /dev/sdXY
```

### Recovery Options

If conversion fails mid-process:

1. **Data is safe** - Files exist on either NTFS or target partition
2. **Check both partitions** - Mount each to verify file locations
3. **Resume or restart** - Use state file to continue or start fresh

---

## Limitations

| Limitation | Details |
|------------|---------|
| **Source filesystem** | Only NTFS supported as source |
| **Single partition** | Converts one partition at a time |
| **Package manager** | Designed for Arch Linux (pacman) |
| **Partition table** | Works with MBR and GPT |
| **Mounted partitions** | Source partition must be unmountable |

---

## Security Considerations

- Always backup critical data before any partition operations
- Test with `--dry-run` first to understand what will happen
- Run on non-critical systems initially
- The script requires root privileges for partition operations

---

## Contributing

Contributions are welcome! Please ensure:

- Code follows bash best practices (`shellcheck` clean)
- TUI elements work across various terminal emulators
- Error handling is comprehensive
- Documentation is updated for new features

### Development Testing

```bash
# Test with dummy mode (no actual disk operations)
sudo ./convert_ntfs_to_linux_fs.sh --dummy-mode

# Test with dry run (shows what would happen)
sudo ./convert_ntfs_to_linux_fs.sh --dry-run
```

---

## Author

**L. Tansley**

---

## License

This project is licensed under the GNU General Public License v3.0 - see the [LICENSE](LICENSE) file for details.

```
This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.
```

---

## Disclaimer

> **WARNING**: This tool performs destructive operations on disk partitions.
>
> - Always backup important data before use
> - Test with `--dry-run` mode first
> - Use on non-critical systems initially
> - Ensure you understand the conversion process
>
> **The author is not responsible for any data loss. Use at your own risk.**

---

## Changelog

### v1.0.0

- Initial release
- Support for ext4, btrfs, xfs, f2fs, reiserfs, jfs
- Interactive TUI with color support
- Iterative conversion with resume capability
- Automatic dependency management
- HDD/SSD detection with smart defragmentation
- Dry-run and dummy modes for testing
