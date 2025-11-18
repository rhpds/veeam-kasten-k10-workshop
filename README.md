# Veeam Workshop Collection

Ansible collection for deploying Veeam Kasten K10 workshops on OpenShift.

## Description

This collection provides automation to deploy and configure Kasten K10 data protection workshop environments on OpenShift, including:
- Kasten K10 operator installation from Red Hat Marketplace
- K10 instance deployment and configuration
- Storage class and volume snapshot class configuration for backup operations
- Multi-user or single-user demo/workshop environments
- Object storage bucket provisioning for backup targets

## Installation

### From Git Repository

```bash
ansible-galaxy collection install git+https://github.com/yourusername/veeam-workshop.git
```

### From Local Source

```bash
ansible-galaxy collection install /path/to/veeam-workshop
```

## Included Content

### Roles

- `veeam.workshop.ocp4_workload_kasten_k10_workshop` - Main role for deploying Kasten K10 workshops

## Usage

### Basic Example

```yaml
---
- name: Deploy Kasten K10 Workshop
  hosts: localhost
  connection: local
  gather_facts: false
  tasks:
    - name: Include Kasten K10 workshop role
      ansible.builtin.include_role:
        name: veeam.workshop.ocp4_workload_kasten_k10_workshop
      vars:
        ACTION: provision
        ocp4_workload_kasten_k10_setup_demo: true
        ocp4_workload_kasten_k10_multi_user: true
        ocp4_workload_kasten_k10_num_users: 30
```

### Single User Demo

```yaml
---
- name: Deploy Kasten K10 Single User Demo
  hosts: localhost
  connection: local
  gather_facts: false
  tasks:
    - name: Include Kasten K10 workshop role
      ansible.builtin.include_role:
        name: veeam.workshop.ocp4_workload_kasten_k10_workshop
      vars:
        ACTION: provision
        ocp4_workload_kasten_k10_setup_demo: true
        ocp4_workload_kasten_k10_multi_user: false
```

### Remove Workshop

```yaml
---
- name: Remove Kasten K10 Workshop
  hosts: localhost
  connection: local
  gather_facts: false
  tasks:
    - name: Include Kasten K10 workshop role
      ansible.builtin.include_role:
        name: veeam.workshop.ocp4_workload_kasten_k10_workshop
      vars:
        ACTION: destroy
```

## Role Variables

See [roles/ocp4_workload_kasten_k10_workshop/defaults/main.yml](roles/ocp4_workload_kasten_k10_workshop/defaults/main.yml) for all available variables.

### Key Variables

- `ACTION`: Either `provision` or `destroy` to deploy or remove the workshop
- `ocp4_workload_kasten_k10_subscription`: Kasten K10 subscription type (default: `k10-kasten-operator-paygo-rhmp`)
- `ocp4_workload_kasten_k10_namespace`: Namespace for Kasten operator (default: `kasten-io`)
- `ocp4_workload_kasten_k10_deploy_k10`: Deploy K10 instance (default: `true`)
- `ocp4_workload_kasten_k10_setup_demo`: Set up demo environment with storage (default: `false`)
- `ocp4_workload_kasten_k10_multi_user`: Configure for multiple users (default: `false`)
- `ocp4_workload_kasten_k10_num_users`: Number of workshop users for multi-user setup (default: `2`)
- `ocp4_workload_kasten_k10_authentication_type`: Authentication type - `openshift` or `htpasswd` (default: `openshift`)

## Requirements

- OpenShift 4.x cluster with cluster-admin access
- Ansible 2.9 or higher
- kubernetes.core collection
- Red Hat Marketplace subscription or entitlement for Kasten K10

## License

GPL-2.0-or-later

## Author Information

Created for Veeam Kasten K10 workshops and demonstrations
