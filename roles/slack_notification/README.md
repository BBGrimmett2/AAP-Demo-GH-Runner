# Ansible Role: slack_notification

Send Slack notifications for automation events (success, failure, warning, info).

## Description

This role provides Slack notification capabilities for AAP automation:
- Formatted messages with color-coded attachments
- Support for success, failure, warning, and info types
- Job context, error messages, and links to logs
- Integration with AAP credential input sources

## Requirements

- Slack webhook URL configured in workspace
- Internet connectivity to slack.com

## Role Variables

### Required Variables

```yaml
# Slack webhook URL (from AAP credential input source)
slack_webhook_url: "https://hooks.slack.com/services/..."

# Notification type
notification_type: "failure"  # Options: success, failure, warning, info

# Job name
job_name: "Deploy AAP Demo"
```

### Optional Variables

```yaml
# Slack channel (default: #aap-demo-notifications)
slack_channel: "#my-channel"

# Job status and context
job_status: "failed"
job_id: "12345"
job_url: "https://controller.example.com/jobs/12345"

# Error message
error_message: "Deployment failed due to timeout"

# Additional context
extra_info:
  runner: "runner-rhel-01"
  commit_sha: "abc123"
```

## Dependencies

None.

## Public Functions (tasks_from)

### send
Send a Slack notification.

```yaml
- name: Send failure notification
  ansible.builtin.import_role:
    name: slack_notification
    tasks_from: send
  vars:
    notification_type: "failure"
    job_name: "Deploy AAP Demo"
    job_status: "failed"
    error_message: "Connection timeout"
```

## Example Playbook

### Notify on Failure

```yaml
---
- name: Deploy with notifications
  hosts: localhost
  gather_facts: false

  tasks:
    - name: Attempt deployment
      ansible.builtin.command: /path/to/deploy.sh
      register: deploy_result
      failed_when: false

    - name: Send failure notification
      ansible.builtin.import_role:
        name: slack_notification
        tasks_from: send
      vars:
        notification_type: "failure"
        job_name: "AAP Demo Deployment"
        job_status: "failed"
        error_message: "{{ deploy_result.stderr }}"
        job_url: "{{ lookup('env', 'JOB_URL') }}"
      when: deploy_result.rc != 0
```

### Notify on Success

```yaml
- name: Send success notification
  ansible.builtin.import_role:
    name: slack_notification
    tasks_from: send
  vars:
    notification_type: "success"
    job_name: "Nightly Health Check"
    job_status: "passed"
    extra_info:
      runners_checked: "2"
      duration: "45 seconds"
```

## Notification Types

- **success** (green) - Job completed successfully
- **failure** (red) - Job failed or encountered errors
- **warning** (yellow) - Job completed with warnings
- **info** (blue) - Informational message

## License

Apache 2.0

## Author

Generated for AAP Demo Runner Management Project
