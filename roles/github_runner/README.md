# Ansible Role: github_runner

Install, configure, and manage GitHub Actions self-hosted runners on RHEL and Windows platforms.

## Description

This role provides comprehensive management of GitHub Actions self-hosted runners with support for:
- RHEL (systemd service management)
- Windows Server (Windows Service management)
- Runner registration and configuration
- Service health monitoring
- Idempotent operations

## Requirements

### RHEL
- RHEL 8.x or 9.x
- systemd
- sudo access for service management
- Internet connectivity to github.com

### Windows
- Windows Server 2019/2022/2025
- PowerShell 5.1 or higher
- .NET Framework 4.7.2+ or .NET Runtime 6.0+
- Administrator access

## Role Variables

### Required Variables

```yaml
# GitHub repository URL where runner will be registered
github_runner_url: "https://github.com/owner/repo"

# GitHub runner registration token (from API or PAT)
github_runner_token: "AQVR..."
```

### Optional Variables

```yaml
# Runner version to install
github_runner_version: "2.311.0"

# Runner name (defaults to hostname)
github_runner_name: "{{ inventory_hostname }}"

# Runner group
github_runner_group: "Default"

# Custom labels (added to default labels)
github_runner_labels:
  - aap-demo
  - podman

# Replace existing runner with same name
github_runner_replace_existing: false

# Ephemeral runner (removed after one job)
github_runner_ephemeral: false

# Disable automatic updates
github_runner_disable_update: false
```

## Dependencies

None.

## Public Functions (tasks_from)

This role follows the "roles as classes" pattern with callable public functions:

### install
Install runner binaries and dependencies.

```yaml
- name: Install runner only
  ansible.builtin.import_role:
    name: github_runner
    tasks_from: install
```

### configure
Register and configure runner with GitHub.

```yaml
- name: Configure runner only
  ansible.builtin.import_role:
    name: github_runner
    tasks_from: configure
  vars:
    github_runner_url: "https://github.com/owner/repo"
    github_runner_token: "{{ vault_runner_token }}"
```

### service
Configure and start runner service.

```yaml
- name: Setup service only
  ansible.builtin.import_role:
    name: github_runner
    tasks_from: service
```

### health_check
Verify runner health and connectivity.

```yaml
- name: Check runner health
  ansible.builtin.import_role:
    name: github_runner
    tasks_from: health_check
```

## Example Playbook

### Full Installation

```yaml
---
- name: Setup GitHub Actions runners
  hosts: github_runners
  gather_facts: true
  
  vars:
    github_runner_url: "https://github.com/RedHatOfficial/aap-demo"
    github_runner_token: "{{ lookup('cyberark_credential', 'github_runner_token') }}"
    github_runner_labels:
      - aap-demo
      - podman

  roles:
    - github_runner
```

### Using Public Functions

```yaml
---
- name: Install and configure runner separately
  hosts: runner-rhel-01
  gather_facts: true
  
  tasks:
    - name: Install runner
      ansible.builtin.import_role:
        name: github_runner
        tasks_from: install
      
    - name: Configure runner
      ansible.builtin.import_role:
        name: github_runner
        tasks_from: configure
      vars:
        github_runner_url: "https://github.com/owner/repo"
        github_runner_token: "{{ runner_token }}"
      
    - name: Setup service
      ansible.builtin.import_role:
        name: github_runner
        tasks_from: service
```

## Platform-Specific Details

### RHEL
- Installs runner to `/opt/actions-runner`
- Creates `runner` service account
- Manages via systemd service: `actions.runner.org.hostname`
- Requires `dotnet-runtime-6.0` package

### Windows
- Installs runner to `C:\actions-runner`
- Runs as SYSTEM account
- Manages via Windows Service
- Requires .NET Runtime 6.0+

## Tags

- `install` - Install runner binaries
- `configure` - Configure and register runner
- `service` - Setup and start service

## License

Apache 2.0

## Author

Generated for AAP Demo Runner Management Project
