# Proxmox Virtual Environment Template

<div align="center">
  <img src="images/logo.png" alt="Dangelmaiers Logo" width="80" height="80">
  <h3 align="center">pve-template</h3>
  <p align="center">
    Standard template for Proxmox Virtual Environment repositories in the Dangelmaiers organization
    <br />
    <a href="#about"><strong>Explore the docs »</strong></a>
    <br />
    <br />
    <a href="#usage">View Usage</a>
    &middot;
    <a href="https://github.com/Dangelmaiers/pve-template/issues/new?labels=bug&template=bug-report---.md">Report Bug</a>
    &middot;
    <a href="https://github.com/Dangelmaiers/pve-template/issues/new?labels=enhancement&template=feature-request---.md">Request Feature</a>
  </p>
</div>

<!-- TABLE OF CONTENTS -->
<details>
  <summary>Table of Contents</summary>
  <ol>
    <li><a href="#about">About</a></li>
    <li><a href="#infrastructure-context">Infrastructure Context</a></li>
    <li><a href="#repository-structure">Repository Structure</a></li>
    <li><a href="#usage">Usage</a></li>
    <li><a href="#naming-conventions">Naming Conventions</a></li>
    <li><a href="#integration-points">Integration Points</a></li>
    <li><a href="#contributing">Contributing</a></li>
    <li><a href="#license">License</a></li>
    <li><a href="#contact">Contact</a></li>
  </ol>
</details>

## About

This repository serves as a template for all Proxmox Virtual Environment (PVE) related repositories in the Dangelmaiers organization. It follows standardized naming conventions and includes required files and directory structures for consistent documentation and management of Proxmox infrastructure.

### Tags and Topics

- `proxmox`
- `virtualization`
- `infrastructure`

## Infrastructure Context

PVE repositories in the Dangelmaiers organization are structured to manage and document the virtualization infrastructure. This includes:

- Core Proxmox configuration
- Network setup
- Storage configurations
- VM and container management
- Cluster administration
- Backup and restoration procedures

These repositories form the foundation for all containerized services and require proper documentation for both setup and maintenance.

## Repository Structure

All PVE repositories follow this standard structure:

```
pve-*/
├── README.md              # Overview and documentation
├── SETUP.md               # Installation and configuration instructions
├── MAINTENANCE.md         # Maintenance procedures
└── config/                # Configuration files
    └── examples/          # Example configuration files
```

## Usage

To use this template for a new Proxmox-related repository:

1. Create a new repository using this template
2. Rename according to the `pve-*` convention (e.g., `pve-network`, `pve-storage`)
3. Update the README.md with specific details
4. Add relevant configuration files to the `config/examples/` directory
5. Document setup procedures in SETUP.md
6. Document maintenance procedures in MAINTENANCE.md

## Naming Conventions

The Dangelmaiers organization uses the following prefix system:

- `pve-*`: Proxmox Virtual Environment related repositories
- `lxc-*`: Container specific configurations and setups
- `doc-*`: Documentation repositories

Examples of PVE repository names:
- `pve-core`: Core Proxmox configuration
- `pve-storage`: Storage configurations
- `pve-network`: Network setup

## Integration Points

PVE repositories typically integrate with:

1. **Other PVE repositories**
   - Network configuration depends on core Proxmox setup
   - Storage configuration builds upon network setup

2. **Container repositories (`lxc-*`)**
   - Provides virtualization infrastructure for containers
   - Defines network access for container services
   - Manages storage resources for containers

3. **Documentation repositories (`doc-*`)**
   - Infrastructure documentation references PVE configuration
   - Maintenance procedures build upon PVE documentation

## Contributing

Contributions should follow the Dangelmaiers organization standards:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/improvement`)
3. Commit your changes (`git commit -m 'Add some improvement'`)
4. Push to the branch (`git push origin feature/improvement`)
5. Open a pull request

## License

Refer to the `LICENSE.txt` file for license information.

## Contact

Infrastructure Team - infrastructure@dangelmaiers.org

---

*This repository follows the Dangelmaiers organization structure standards. For more information, refer to the [doc-infrastructure](https://github.com/Dangelmaiers/doc-infrastructure) repository.*

