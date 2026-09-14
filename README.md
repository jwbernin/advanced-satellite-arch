# Ansible Collection - infra.advanced_satellite_arch

Documentation for the collection.

## Requirements

The supported collections that contains the modules are required for this collection to work, you can copy this `requirements.yml` file example.
When installing the collection, the collection will validate and attempt to install these dependecies.

```yaml
---
collections:
  - ansible.posix
  - community.crypto
  - community.general
  - redhat.rhel_system_roles
  - redhat.satellite
  - redhat.satellite_operations
```

## Developing this collection

### Building the collection

Versions matter, in order to build this collection, validate the version in [galaxy.yml](./galaxy.yml) is updated to the correct version before deploying.

```yaml
ansible-galaxy collection build infra.advanced_satellite_arch
```

## Installing this collection

With the infra.advanced_satellite_arch-X.Y.Z.tar.gz

```yaml
ansible-galaxy collection install infra.advanced_satellite_arch-X.Y.Z.tar.gz
```

## Roles

### satellite_install
[infra.advanced_satellite_arch.satellite_install](roles/satellite_install/)

#### Example Playbook

```yaml
---
- name: Configure and install connected Satellite
  hosts: connected_satellite
  gather_facts: true
  become: true

  vars:
    satellite_server_url: "satellite.local" # Connected Satellite FQDN
    satellite_username: "admin" # Example username
    satellite_password: "redhat123" # Example password
    satellite_organization: "home" # Satellite organization

  tasks:
    - name: Assert that connected
      ansible.builtin.assert:
        that: satellite_rhn_connected is true

    - name: INCLUDE_ROLE | infra.advanced_satellite_arch.satellite_install
      ansible.builtin.include_role:
        name: infra.advanced_satellite_arch.satellite_install
...
```

### capsule_install
[infra.advanced_satellite_arch.capsule_install](roles/capsule_install/)

#### Example Playbook

```yaml
---
- name: Install and configure Satellite capsules
  hosts: capsules
  gather_facts: true
  become: true

  vars:
    satellite_server_url: "satellite.local" # Connected Satellite FQDN
    satellite_username: "admin" # Example username
    satellite_password: "redhat123" # Example password
    satellite_organization: "home" # Satellite organization

  tasks:
    - name: INCLUDE_ROLE | infra.advanced_satellite_arch.capsule_install
      ansible.builtin.include_role:
        name: infra.advanced_satellite_arch.capsule_install
...
```

### content_view_management
[infra.advanced_satellite_arch.content_view_management](roles/content_view_management/)

#### Example Playbook

```yaml
---
- name: Content View Management
  hosts: localhost # Run from localhost, modules will excute with API to Satellite
  gather_facts: true

  vars:
    satellite_server_url: "satellite.local"
    satellite_username: "admin"
    satellite_password: "redhat123"
    satellite_organization: "home"
    content_view_version_number: "20250616" # Hard code a date or use "{{ ansible_date_time['date'] | split('-') | join('') }}" for automatic dates (this is default)
    content_view_package_filter_end_date: "2025-06-16" # Hard code a date
    content_view_lifecycle_environments: satinfra # Specify lifecyle environment
    content_view_specific_packages:
      # This package is a part of the BaseOS RPMs 9 repository, with specific version
      - name: kernel
        version: 5.14.0
        repositories:
          - name: "Red Hat Enterprise Linux 9 for x86_64 - BaseOS RPMs 9"
            product: "Red Hat Enterprise Linux for x86_64"
      # This package is a part of the AppStream RPMs 9 repository, no specific version
      - name: vim-common
        repositories:
          - name: "Red Hat Enterprise Linux 9 for x86_64 - AppStream RPMs 9"
            product: "Red Hat Enterprise Linux for x86_64"
      # This is just package for all repositories and no specific version, even though it should be from EPEL
      - name: terminator 
        repositories: []
  
  tasks:
    - name: INCLUDE_ROLE | infra.advanced_satellite_arch.content_view_management
      ansible.builtin.include_role:
        name: infra.advanced_satellite_arch.content_view_management
...
```

### export_content_library
[infra.advanced_satellite_arch.export_content_library](roles/export_content_library/)

#### Example Playbook

```yaml
---
- name: Export Library
  hosts: localhost # Run from localhost, modules will excute with API to Satellite
  gather_facts: true

  vars:
    satellite_server_url: "satellite.local" # Connected Satellite FQDN
    satellite_username: "admin" # Example username
    satellite_password: "redhat123" # Example password
    satellite_organization: "home" # Satellite organization
    satellite_export_library_format: importable # importable creates *.tar, syncable needs to be hosted via web server for import
    satellite_export_destination_server_fqdn: disconnected.satellite.local # Optional

  tasks:
    - name: INCLUDE_ROLE | infra.advanced_satellite_arch.export_content_library
      ansible.builtin.include_role:
        name: infra.advanced_satellite_arch.export_content_library
...
```

### export_content_view
[infra.advanced_satellite_arch.export_content_view](roles/export_content_view/)

#### Example Playbook

```yaml
---
- name: Export Content View
  hosts: localhost # Run from localhost, modules will excute with API to Satellite
  gather_facts: true

  vars:
    satellite_server_url: "satellite.local" # Connected Satellite FQDN
    satellite_username: "admin" # Example username
    satellite_password: "redhat123" # Example password
    satellite_organization: "home" # Satellite organization
    satellite_export_content_view_name: satellite6 # Name of the content view
    satellite_export_content_view_version_number: "20250618.1" # Version number of content view to be exported
    satellite_export_content_view_format: importable # importable creates *.tar, syncable needs to be hosted via web server for import
    satellite_export_destination_server_fqdn: disconnected.satellite.local # Optional

  tasks:
    - name: INCLUDE_ROLE | infra.advanced_satellite_arch.export_content_view
      ansible.builtin.include_role:
        name: export_content_view
...
```

### import_content_library
[infra.advanced_satellite_arch.import_content_library](roles/import_content_library/)

#### Example Playbook

```yaml
---
- name: Import Content Library
  hosts: localhost # Run from localhost, modules will excute with API to Satellite
  gather_facts: true

  vars:
    satellite_server_url: "disconnected.satellite.local" # Disconnected Satellite FQDN
    satellite_username: "admin" # Example username
    satellite_password: "redhat123" # Example password
    satellite_organization: "home" # Satellite organization

  tasks:
    - name: INCLUDE_ROLE | infra.advanced_satellite_arch.import_content_library
      ansible.builtin.include_role:
        name: infra.advanced_satellite_arch.import_content_library
...
```

### import_content_view
[infra.advanced_satellite_arch.import_content_view](roles/import_content_view/)

#### Example Playbook

```yaml
---
- name: Import Content View
  hosts: localhost # Run from localhost, modules will excute with API to Satellite
  gather_facts: true

  vars:
    satellite_server_url: "disconnected.satellite.local" # Disconnected Satellite FQDN
    satellite_username: "admin" # Example username
    satellite_password: "redhat123" # Example password
    satellite_organization: "home" # Satellite organization

  tasks:
    - name: INCLUDE_ROLE | infra.advanced_satellite_arch.import_content_view
      ansible.builtin.include_role:
        name: infra.advanced_satellite_arch.import_content_view
...
```
