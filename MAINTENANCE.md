
# Proxmox Maintenance Procedures

This document outlines standardized maintenance procedures for Proxmox Virtual Environment (PVE) repositories in the Dangelmaiers organization.

## Regular Maintenance

### Daily Tasks

- Check Proxmox cluster status
  ```sh
  pvecm status
  ```
- Verify all nodes are online
  ```sh
  pvenode status
  ```
- Monitor resource usage
  ```sh
  pveperf
  ```

### Weekly Tasks

- Check for Proxmox updates
  ```sh
  apt update
  apt list --upgradable
  ```
- Review system logs
  ```sh
  journalctl -p err -b
  ```
- Verify backup success
  ```sh
  vzdump list-snapshots
  ```

### Monthly Tasks

- Apply non-critical updates
  ```sh
  apt update && apt upgrade -y
  ```
- Clean up old container templates
  ```sh
  pveam prune-downloaded
  ```
- Audit user accounts and access

## Backup Procedures

### VM/Container Backup Schedule

| Environment | Frequency | Retention | Storage |
|-------------|-----------|-----------|---------|
| Production | Daily | 30 days | backup-storage |
| Development | Weekly | 14 days | backup-storage |
| Templates | Monthly | 90 days | template-storage |

### Backup Commands

```sh
# Backup all VMs and containers
vzdump --all --compress zstd --mode snapshot --storage backup-storage

# Backup specific VM
vzdump 100 --compress zstd --mode snapshot --storage backup-storage
```

### Restore Procedures

1. Locate the backup file
   ```sh
   find /mnt/backup-storage -name "vzdump-qemu-*"
   ```

2. Restore VM/container
   ```sh
   qmrestore /mnt/backup-storage/vzdump-qemu-100-2025_04_18-00_00_00.vma.zst 100 --storage local-lvm
   ```

3. Verify restoration
   ```sh
   qm status 100
   ```

## Upgrade Procedures

### Proxmox Version Upgrade

1. Pre-upgrade checks
   ```sh
   # Check current version
   pveversion -v
   
   # Update package information
   apt update
   ```

2. Backup current state
   ```sh
   # Create VM backups
   vzdump --all --compress zstd --mode snapshot --storage backup-storage
   
   # Backup Proxmox configuration
   tar -czf /root/pve-config-backup-$(date +%Y%m%d).tar.gz /etc/pve
   ```

3. Perform upgrade
   ```sh
   # For major version upgrades, modify repository sources first
   nano /etc/apt/sources.list.d/pve-enterprise.list
   
   # Then upgrade
   apt update
   apt dist-upgrade
   ```

4. Post-upgrade verification
   ```sh
   # Check version
   pveversion -v
   
   # Check services
   systemctl status pve*
   
   # Check cluster
   pvecm status
   ```

## Monitoring

### Key Metrics

- CPU usage: Alert threshold 80%
- Memory usage: Alert threshold 85%
- Disk usage: Alert threshold 85%
- Cluster quorum status

### Alert Configuration

Configure monitoring alerts in `/etc/pve/status.cfg`:

```
mailto: admin@dangelmaiers.org
disk_usage: 0.85
cpu_usage: 0.8
memory_usage: 0.85
```

## Troubleshooting

### Common Issues

| Issue | Diagnosis | Resolution |
|-------|-----------|------------|
| Node not in cluster | `pvecm status` shows node as unknown | Restart services with `systemctl restart pve-cluster` |
| Storage unavailable | Check with `pvesm status` | Verify underlying storage system and connections |
| Network issues | Check with `ip a` and `bridge link show` | Verify network configuration and physical connections |

### Log Locations

Important log locations for troubleshooting:

- Proxmox logs: `/var/log/pve/`
- Cluster logs: `/var/log/pve-cluster/`
- System logs: `/var/log/syslog`
- Kernel logs: `dmesg`

## Emergency Procedures

### Node Failure

1. Assess the situation
   ```sh
   # From another node
   pvecm status
   ```

2. If node is down but not failed in the cluster
   ```sh
   # From the affected node (after reboot)
   systemctl restart pve-cluster
   ```

3. If node needs to be removed from cluster
   ```sh
   # From another node
   pvecm delnode failednode
   ```

### Cluster Recovery

For quorum loss:

1. If majority of nodes are available
   ```sh
   # From any remaining node
   pvecm expected 1
   ```

2. If majority of nodes are unavailable (ONLY in disaster recovery)
   ```sh
   # From the remaining node
   systemctl stop pve-cluster corosync
   pmxcfs -l
   rm /etc/corosync/*
   rm /etc/pve/corosync.conf
   rm -rf /var/lib/corosync/*
   systemctl start pmxcfs
   pvecm create new-cluster
   ```

---

*This document follows the Dangelmaiers organization documentation standards. For questions or issues, contact the infrastructure team.*

