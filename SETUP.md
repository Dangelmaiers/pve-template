
# Proxmox Setup Guide

This document provides standardized setup instructions for Proxmox Virtual Environment repositories in the Dangelmaiers organization.

## Prerequisites

- Proxmox Virtual Environment 7.x or higher
- Admin access to the Proxmox cluster
- Network connectivity with appropriate VLANs configured
- Storage infrastructure (local LVM, ZFS, NFS, or Ceph)
- SSH access to the Proxmox nodes

## Installation

1. Initial setup
   ```sh
   # Clone this repository
   git clone https://github.com/dangelmaiers/pve-[specific-name].git
   cd pve-[specific-name]
   ```

2. Configuration
   ```sh
   # Copy example configuration files
   cp config/examples/proxmox.conf.example config/proxmox.conf
   # Modify configuration to match your environment
   nano config/proxmox.conf
   ```

3. Apply Configuration
   ```sh
   # Example command to apply configuration
   ./apply-config.sh --config=config/proxmox.conf --target=proxmox.cluster.local
   ```

## VM/Container Configuration

### VM Templates

Standard VM template configuration:

```
cores: 2
memory: 4096
net0: virtio,bridge=vmbr0
scsi0: local-lvm:32
ostype: l26
```

### Container Settings

Standard container configuration:

```
memory: 2048
swap: 512
net0: name=eth0,bridge=vmbr0,ip=dhcp
storage: local-lvm:8
```

### Resource Management

- CPU limits:
  - Production: min 2 cores
  - Development: min 1 core
- Memory allocation:
  - Production: min 4GB
  - Development: min 2GB
- Storage guidelines:
  - System disk: 32GB minimum
  - Data volumes: Separate from system

## Network Setup

### VLAN Configuration

| VLAN ID | Purpose | Subnet | Gateway |
|---------|---------|--------|---------|
| 10 | Management | 10.10.10.0/24 | 10.10.10.1 |
| 20 | Production | 10.20.20.0/24 | 10.20.20.1 |
| 30 | Development | 10.30.30.0/24 | 10.30.30.1 |
| 40 | Storage | 10.40.40.0/24 | 10.40.40.1 |

### Network Bridges

Standard bridge configuration:

```
auto vmbr0
iface vmbr0 inet static
    address 10.10.10.2/24
    gateway 10.10.10.1
    bridge-ports eno1
    bridge-stp off
    bridge-fd 0
```

## Storage Configuration

### Storage Types

| Type | Purpose | Backup Enabled | Thin Provisioning |
|------|---------|----------------|-------------------|
| local-lvm | VM disks | Yes | Yes |
| cephfs | Shared storage | Yes | No |
| nfs-share | ISO storage | No | N/A |

### ZFS Configuration

Recommended ZFS pool configuration:

```
# Create ZFS pool
zpool create -f -o ashift=12 tank mirror sda sdb

# Create datasets
zfs create tank/vm-data
zfs create tank/container-data
```

## Verification

Steps to verify the configuration was successfully applied:

1. Check Proxmox service status
   ```sh
   systemctl status pveproxy
   systemctl status pvedaemon
   ```

2. Verify network configuration
   ```sh
   ip a
   bridge link show
   ```

3. Verify storage configuration
   ```sh
   pvesm status
   ```

## Troubleshooting

Common issues and their solutions:

| Issue | Solution |
|-------|----------|
| VM won't start | Check resource availability and logs in `/var/log/pve/` |
| Network connectivity issues | Verify bridge configuration and physical connections |
| Storage not available | Check storage service status and connectivity |

## Next Steps

- See [MAINTENANCE.md](./MAINTENANCE.md) for maintenance procedures
- Reference [doc-infrastructure](https://github.com/Dangelmaiers/doc-infrastructure) for comprehensive documentation
- Review example configurations in the `config/examples/` directory

---

*This document follows the Dangelmaiers organization documentation standards. For questions or issues, contact the infrastructure team.*

