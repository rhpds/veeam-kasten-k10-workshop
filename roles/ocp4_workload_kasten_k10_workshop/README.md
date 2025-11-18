# ocp4_workload_kasten_k10_workshop

Ansible role to deploy Kasten K10 data protection platform on OpenShift 4.

## Description

This role automates the deployment of Kasten K10 for workshop or demo environments, including:

- **Kasten K10 Operator**: Installs the Kasten K10 operator from Red Hat Marketplace
- **K10 Instance**: Deploys and configures a Kasten K10 instance with authentication
- **Storage Configuration**: Annotates storage classes and volume snapshot classes for backup operations
- **Demo Environment**: Optional setup for multi-user workshops with object storage buckets for backup targets

## Requirements

- OpenShift 4.x cluster with cluster-admin privileges
- Ansible 2.9 or higher
- `kubernetes.core` collection
- Red Hat Marketplace subscription or entitlement for Kasten K10

## Role Variables

All variables are defined in [defaults/main.yml](defaults/main.yml).

### Core Variables

| Variable | Default | Description |
|----------|---------|-------------|
| `ACTION` | N/A | **Required**. Either `provision` or `destroy` |
| `ocp4_workload_kasten_k10_namespace` | `kasten-io` | Namespace to deploy Kasten operator |

### Kasten Operator Configuration

| Variable | Default | Description |
|----------|---------|-------------|
| `ocp4_workload_kasten_k10_subscription` | `k10-kasten-operator-paygo-rhmp` | Subscription name. Options: `k10-kasten-operator-paygo-rhmp`, `k10-kasten-operator-rhmp`, `k10-kasten-operator-term-rhmp` |
| `ocp4_workload_kasten_k10_channel` | `stable` | Operator subscription channel |
| `ocp4_workload_kasten_k10_automatic_install_plan_approval` | `true` | Auto-approve install plans |
| `ocp4_workload_kasten_k10_starting_csv` | `""` | Starting CSV version (leave empty for latest) |

### K10 Instance Configuration

| Variable | Default | Description |
|----------|---------|-------------|
| `ocp4_workload_kasten_k10_deploy_k10` | `true` | Deploy K10 instance after operator installation |
| `ocp4_workload_kasten_k10_name` | `k10` | Name of the K10 instance |
| `ocp4_workload_kasten_k10_authentication_type` | `openshift` | Authentication type. Options: `openshift`, `htpasswd` |

### Demo/Workshop Configuration

| Variable | Default | Description |
|----------|---------|-------------|
| `ocp4_workload_kasten_k10_setup_demo` | `false` | Set up demo environment with storage and permissions |
| `ocp4_workload_kasten_k10_multi_user` | `false` | Configure for multiple users vs single user |
| `ocp4_workload_kasten_k10_num_users` | `2` | Number of users for multi-user setup |
| `ocp4_workload_kasten_k10_objectbucket_user_base` | `user` | Base name for user accounts |

### Storage Configuration

| Variable | Default | Description |
|----------|---------|-------------|
| `ocp4_workload_kasten_k10_storageclasses` | `['ocs-external-storagecluster-ceph-rbd']` | Storage classes to annotate for block mode exports |
| `ocp4_workload_kasten_k10_volumesnapshotclasses` | `['ocs-external-storagecluster-rbdplugin-snapclass', 'ocs-external-storagecluster-cephfsplugin-snapclass']` | Volume snapshot classes to annotate as backup targets |
| `ocp4_workload_kasten_k10_objectbucket_name` | `kastenbackups` | Name for object bucket claim |
| `ocp4_workload_kasten_k10_objectbucket_storage_class` | `openshift-storage.noobaa.io` | Storage class for object buckets |
| `ocp4_workload_kasten_k10_objectbucket_namespace` | `backuptarget` | Namespace for single-user object bucket |
| `ocp4_workload_kasten_k10_objectbucket_namespace_base` | `backuptarget-{{ ocp4_workload_kasten_k10_objectbucket_user_base }}` | Namespace base for multi-user object buckets |

See [defaults/main.yml](defaults/main.yml) for complete list of configuration options.

## Dependencies

- `install_operator` role (for operator installation)

## Example Playbook

### Deploy Kasten K10 Workshop

```yaml
---
- name: Deploy Kasten K10 Workshop
  hosts: localhost
  connection: local
  gather_facts: false
  tasks:
    - name: Deploy Kasten K10
      ansible.builtin.include_role:
        name: ocp4_workload_kasten_k10_workshop
      vars:
        ACTION: provision
        ocp4_workload_kasten_k10_setup_demo: true
        ocp4_workload_kasten_k10_multi_user: true
        ocp4_workload_kasten_k10_num_users: 30
```

### Deploy Single User Demo

```yaml
---
- name: Deploy Kasten K10 Single User Demo
  hosts: localhost
  connection: local
  gather_facts: false
  tasks:
    - name: Deploy Kasten K10
      ansible.builtin.include_role:
        name: ocp4_workload_kasten_k10_workshop
      vars:
        ACTION: provision
        ocp4_workload_kasten_k10_setup_demo: true
        ocp4_workload_kasten_k10_multi_user: false
        ocp4_workload_kasten_k10_authentication_type: htpasswd
```

### Remove Kasten K10

```yaml
---
- name: Remove Kasten K10 Workshop
  hosts: localhost
  connection: local
  gather_facts: false
  tasks:
    - name: Remove Kasten K10
      ansible.builtin.include_role:
        name: ocp4_workload_kasten_k10_workshop
      vars:
        ACTION: destroy
```

## Task Files

- `main.yml` - Entry point that routes to provision or destroy tasks
- `workload.yml` - Main provisioning tasks for operator and K10 instance

## License

GPL-2.0-or-later

## Author Information

Created for Kasten K10 workshops and demonstrations
