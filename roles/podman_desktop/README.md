# Ansible Role: podman_desktop

Install and configure Podman Desktop on RHEL and Windows platforms for AAP Demo testing.

## Description

This role provides Podman Desktop installation and configuration with support for:
- RHEL (Flatpak installation)
- Windows Server (MSI installation with WSL2)
- Podman machine initialization and resource allocation
- Red Hat registry authentication for pulling AAP execution environments

## Requirements

### RHEL
- RHEL 8.x or 9.x
- Flatpak support
- sudo access
- Internet connectivity to flathub.org

### Windows
- Windows Server 2019/2022/2025
- WSL2 enabled and configured
- Administrator access
- Internet connectivity to github.com

## Role Variables

### Optional Variables

```yaml
# Podman Desktop version
podman_desktop_version: "latest"

# Podman machine resource allocation
podman_machine_cpus: 2
podman_machine_memory: 4096  # MB
podman_machine_disk_size: 100  # GB

# Red Hat registry authentication (from AAP credential input source)
redhat_registry_username: ""
redhat_registry_password: ""
```

## Dependencies

None.

## Public Functions (tasks_from)

### install
Install Podman Desktop binaries.

```yaml
- name: Install Podman Desktop only
  ansible.builtin.import_role:
    name: podman_desktop
    tasks_from: install
```

### configure
Configure Podman machine and registry authentication.

```yaml
- name: Configure Podman Desktop only
  ansible.builtin.import_role:
    name: podman_desktop
    tasks_from: configure
  vars:
    redhat_registry_username: "{{ vault_redhat_user }}"
    redhat_registry_password: "{{ vault_redhat_pass }}"
```

## Example Playbook

```yaml
---
- name: Setup Podman Desktop on runners
  hosts: github_runners
  gather_facts: true
  
  vars:
    podman_machine_cpus: 4
    podman_machine_memory: 8192
    redhat_registry_username: "{{ lookup('cyberark_credential', 'redhat_registry_username') }}"
    redhat_registry_password: "{{ lookup('cyberark_credential', 'redhat_registry_password') }}"

  roles:
    - podman_desktop
```

## Platform-Specific Details

### RHEL
- Installs via Flatpak from Flathub
- Requires flatpak package
- Creates podman machine with specified resources
- Podman config at `~/.config/containers/`

### Windows
- Downloads MSI installer from GitHub releases
- Requires WSL2 to be installed and running
- Creates podman machine in WSL2
- Podman config at `%USERPROFILE%\.config\containers\`

## License

Apache 2.0

## Author

Generated for AAP Demo Runner Management Project
