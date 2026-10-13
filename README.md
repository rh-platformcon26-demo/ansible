# ansible

Playbooks Ansible for the [rh-platformcon26-demo](https://github.com/rh-platformcon26-demo) organization.

**Convention:** one directory per playbook at the repository root. Each directory is self-contained (playbook, roles, vars, requirements).

## Playbooks

| Directory | Description |
|---|---|
| [ec2-postgresql/](ec2-postgresql/) | Provision an AWS security group + Fedora EC2 instance and install PostgreSQL (localhost only) |

## Adding a playbook

Create a new top-level directory (for example `my-playbook/`) with its own `playbooks/`, `roles/`, `requirements.yml`, and `README.md`.
