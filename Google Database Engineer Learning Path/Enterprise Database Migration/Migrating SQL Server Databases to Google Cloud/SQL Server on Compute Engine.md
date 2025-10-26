Basically relocating server from local machine to Compute Engine VM

# Shielded VM features
## Secure Boot
Trusted software: Only authentic and unmodified software can run on VMs
Signature verification: UEFI firmware verifies digital signature of all boot components

## virtual Trusted Platform Module
A virtualized chip that enables Measured Boot, recording measurements of VM boot components to create integrity policy baseline

## Integrity Monitoring
Monitors VM's integrity policy baseline; should current boot state deviate from the baseline, server is halted and administrators are alerted 


# Best practices
Security - Use Advanced Firewall, OS's default network settings, installing antivirus software
Backup - Dedicate SSD persistent disk for tempdb and Windows paging files, and if budget allows it dedicate SSD for log and data files, plan regular backups with SQL server agent


