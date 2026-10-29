# Fedora Initial Configuration Role

## Description

This role performs initial configuration of Fedora servers including:
- System timezone configuration
- Hostname setup
- Base package installation
- SSH hardening
- Firewall configuration
- Optional system updates

## Requirements

- Fedora Linux target system
- Ansible 2.9 or higher
- `community.general` collection for timezone module

## Role Variables

### Required Variables
None - all variables have sensible defaults

### Optional Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `fedora_timezone` | `America/New_York` | System timezone to configure |
| `fedora_base_packages` | See defaults | List of base packages to install |
| `fedora_disable_selinux` | `false` | Whether to disable SELinux (not recommended) |
| `fedora_configure_firewall` | `true` | Whether to configure firewalld |
| `fedora_set_hostname` | `true` | Whether to set hostname from inventory |
| `fedora_update_system` | `false` | Whether to perform system updates |
| `fedora_reboot_if_required` | `false` | Whether to reboot after updates if needed |
| `fedora_configure_ssh` | `true` | Whether to configure SSH daemon |
| `fedora_ssh_password_auth` | `false` | Allow password authentication for SSH |
| `fedora_ssh_permit_root_login` | `no` | SSH root login setting (yes/no/prohibit-password) |

## Dependencies

None

## Example Playbook

```yaml
---
- name: Configure Fedora servers
  hosts: fedora_servers
  become: true

  tasks:
    - name: Apply initial Fedora configuration
      ansible.builtin.import_role:
        name: fedora_initial_config
      vars:
        fedora_timezone: "UTC"
        fedora_update_system: true
```

## Execution from AAP

This role can be executed from AAP using the `playbooks/fedora_initial_config.yml` playbook.

### Extra Variables for AAP Job Template

```yaml
target_hosts: fedora_servers
fedora_update_system: true
fedora_timezone: "America/Chicago"
```
