# Azure VM Setup

## Objective

Create a Linux environment on Microsoft Azure for running the DevOps/SRE resilience lab.

## Environment

- Cloud: Microsoft Azure
- Operating System: Ubuntu 24.04 LTS
- Memory: approximately 15 GiB
- Root disk: approximately 29 GiB
- Remote access: SSH

## Initial Setup

System packages were updated and basic engineering tools were installed.

```bash
sudo apt update
sudo apt upgrade -y
sudo apt install -y git curl wget make jq ca-certificates gnupg
```

Verified tools included:

- Git 2.43.0
- GNU Make 4.3

## SSH Access

The VM is administered from a Windows workstation using SSH public-key authentication.

During setup, SSH initially rejected the private key because its Windows file permissions were too permissive. The ACL was corrected before reconnecting.

## Resource Verification

```bash
nproc
free -h
df -h /
```

## Security Considerations

Management and application ports should not be unnecessarily exposed to the public internet.

SSH tunneling is preferred for accessing internal lab dashboards.

## What I Learned

- Azure VM provisioning basics
- SSH key authentication
- Linux package management
- Private-key permission requirements
- Basic Linux resource inspection
