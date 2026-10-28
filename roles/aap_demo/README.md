# Ansible Role: aap_demo

Deploy and test AAP Demo application from RedHatOfficial/aap-demo repository.

## Description

This role provides AAP Demo deployment and testing capabilities:
- Clone and deploy AAP Demo from GitHub
- Run deployment and status tests
- Monitor deployment health
- Clean up deployments

## Requirements

- git
- podman (installed and configured)
- Internet connectivity to github.com
- Sufficient disk space for AAP Demo containers

## Role Variables

### Optional Variables

```yaml
# AAP Demo repository
aap_demo_repo_url: "https://github.com/RedHatOfficial/aap-demo.git"
aap_demo_branch: "main"
aap_demo_workdir: "/tmp/aap-demo"

# Timeouts
aap_demo_deploy_timeout: 600  # seconds
aap_demo_status_timeout: 120  # seconds

# Health checks
aap_demo_health_check_retries: 10
aap_demo_health_check_delay: 30  # seconds
```

## Dependencies

- podman_desktop role (recommended for registry authentication)

## Public Functions (tasks_from)

### deploy
Deploy AAP Demo application.

```yaml
- name: Deploy AAP Demo only
  ansible.builtin.import_role:
    name: aap_demo
    tasks_from: deploy
```

### status
Check AAP Demo deployment status.

```yaml
- name: Check AAP Demo status only
  ansible.builtin.import_role:
    name: aap_demo
    tasks_from: status
```

### test
Run AAP Demo test suite.

```yaml
- name: Test AAP Demo
  ansible.builtin.import_role:
    name: aap_demo
    tasks_from: test
  vars:
    test_type: "status"  # Options: deploy, status, all
```

### cleanup
Remove AAP Demo deployment.

```yaml
- name: Clean up AAP Demo
  ansible.builtin.import_role:
    name: aap_demo
    tasks_from: cleanup
```

## Example Playbook

### Full Deployment and Test

```yaml
---
- name: Deploy and test AAP Demo
  hosts: github_runners
  gather_facts: false
  
  roles:
    - aap_demo
```

### Status Check Only

```yaml
---
- name: Check AAP Demo status
  hosts: localhost
  gather_facts: false
  
  tasks:
    - name: Get status
      ansible.builtin.import_role:
        name: aap_demo
        tasks_from: status
```

### Cleanup

```yaml
---
- name: Clean up AAP Demo
  hosts: localhost
  gather_facts: false
  
  tasks:
    - name: Remove deployment
      ansible.builtin.import_role:
        name: aap_demo
        tasks_from: cleanup
```

## Tags

- `deploy` - Deploy AAP Demo
- `test` - Run tests

## Testing

The role supports two test types:

1. **deploy** - Tests AAP Demo deployment process
2. **status** - Tests AAP Demo status check functionality

Set via `test_type` variable:
```yaml
test_type: "deploy"  # or "status" or "all" (default)
```

## License

Apache 2.0

## Author

Generated for AAP Demo Runner Management Project
