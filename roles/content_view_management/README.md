# Red Hat Satellite - Content View Management

Ansible modules for interacting with the Satellite API creating content views.

## Requirements
Ansible 2.15 or higher

Red Hat Enterprise Linux 8 or higher

Valid Red Hat Subscriptions

## Variables

### Satellite Settings
|Variable Name|Default Value|Required|Type|Description|Example|
|:---|:---|:---:|:---:|:---|:---|
|`satellite_server_url`|"satellite.fqdn"|yes|string|Satellite server FQDN|satellite.domain.com|
|`satellite_username`|"admin"|yes|string|Username to access Satellite server|admin|
|`satellite_password`|"changeme"|yes|string|Password for Username that will be used to access Satellite server|changeme|
|`satellite_organization`|"Default Organization"|yes|string|Organization that this role be applied against|Default Org|

### Optional Settings
|Variable Name|Default Value|Required|Type|Description|Example|
|:---|:---|:---:|:---:|:---|:---|
|`satellite_validate_certs`|`false`|no|boolean|This setting validates certificate when using redhat.satellite modules||
|`satellite_operation_timeout`|7200|no|integer|Timeout in seconds||
|`satellite_poll_interval`|30|no|integer|Polling in seconds||

### Content View Settings
|Variable Name|Default Value|Required|Type|Description|Example|
|:---|:---|:---:|:---:|:---|:---|
|`content_view_name`|"satellite6"|yes|string|Content view name to search for, to execute filter against|satellite6|
|`content_view_version_number`|"`{{ ansible_date_time['date'] \| split('-') \| join('') }}`"|yes|string|Version number to be created, unfiltered |satellite6|
|`content_view_version_minor_rel_number`|"1"|yes|string|Minor version number to be created with filter|satellite6|
|`content_view_lifecycle_environments`|""|no|list|Leave empty to apply to Library, or set specific lifecycle environment|satinfra|
|`content_view_lifecycle_environments_default`|""|no|list|Define default published content view list|Library|
|`content_view_repositories`|""|no|list|Leave empty to apply to repositories, or set specific repository|EPEL9_X86|
|`content_view_original_packages`|`true`|yes|bool|To include all RPMs with no errata||
|`content_view_inclusion`|`true`|yes|bool|Create an include filter; false is exclude filter||
|`content_view_promote`|`true`|yes|bool|Promote content view version||
|`content_view_filtered_promote`|`false`|yes|bool|Promote filtered content view version||
|`content_view_date_type`|updated|yes|string|issued or updated||
|`content_view_filter_type_rpm`|rpm|yes|string|rpm, package_group, erratum, docker, modulemd, deb||
|`content_view_filter_type_erratum`|erratum|yes|string|rpm, package_group, erratum, docker, modulemd, deb||
|`content_view_erratum_type`|erratum|no|list|bugfix, security, enhancement are set by default||
|`content_view_filter_name_by_date`|"`Filter by date - {{ content_view_package_filter_end_date }}`"|yes|string|Name of content view filter|Filter by date - 2025-05-22|
|`content_view_filter_rule_name_rpm_specific`|"RPM Filtered"|yes|string|Name of content view rule filter||
|`content_view_filter_rule_name_all_repositories`|"`{{ content_view_filter_rule_name_rpm_specific }}` - All Repositories"|yes|string|Name of content view rule filter||
|`content_view_filter_description`|"`Published via AAP {{ ansible_date_time['date'] }} at {{ ansible_date_time['time'] }}`"|yes|string|Description of content view filter|	Published via AAP 2025-05-25 at 00:00:00|
|`content_view_publish_description`|"`Published via AAP {{ ansible_date_time['date'] }} at {{ ansible_date_time['time'] }}`"|yes|string|Description of publish version|	Published via AAP 2025-05-25 at 00:00:00|
|`content_view_package_filter_end_date`|"`{{ ansible_date_time['date'] \| split('-') \| join('') }}`"|yes|string|Date format in YYYY-MM-DD||
|`content_view_specific_packages`|""|yes|list|Specific packages to be included in content view filter|`see below`|


#### Content View Lifecyle Environments

Default list will include all lifecycle environments, eg. Library

```yaml
content_view_lifecycle_environments:
  - satinfra
```

#### Content View Repositories

Default list will include all repositories in content view filter

```yaml
content_view_content_view_repositories:
  - EPEL9_X86
```

#### Content View Specific Packages

```yaml
content_view_specific_packages:
  - name: kernel
    version: 5.14.0 # Include version if you want specific version to be added
    repositories: # Include the specific repository the RPM is found
      - name: "Red Hat Enterprise Linux 9 for x86_64 - BaseOS RPMs 9"
        product: "Red Hat Enterprise Linux for x86_64"
  - name: kernel
    repositories: [] # Include all repositories for this RPM
```

#### **`playbook.yml`**  

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
    content_view_version_number: "20250616" # Hard code a date or use "{{ ansible_date_time['date'] | split('-') | join('') }}" for automatic dates
    content_view_package_filter_end_date: "2025-06-16"
    content_view_lifecycle_environments: satinfra
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
    - name: INCLUDE_ROLE | content_view_management
      ansible.builtin.include_role:
        name: content_view_management

```

## Documentation

### Red Hat Satellite Collection

[Link](https://console.redhat.com/ansible/automation-hub/repo/published/redhat/satellite/)

#### Modules

- [redhat.satellite.content_view_filter](https://console.redhat.com/ansible/automation-hub/repo/published/redhat/satellite/content/module/content_view_filter/)
- [redhat.satellite.content_view_filter_rule](https://console.redhat.com/ansible/automation-hub/repo/published/redhat/satellite/content/module/content_view_filter_rule/)
- [redhat.satellite.content_view_info](https://console.redhat.com/ansible/automation-hub/repo/published/redhat/satellite/content/module/content_view_info/)
- [redhat.satellite.content_view_version](https://console.redhat.com/ansible/automation-hub/repo/published/redhat/satellite/content/module/content_view_version/)
- [redhat.satellite.content_view_version_info](https://console.redhat.com/ansible/automation-hub/repo/published/redhat/satellite/content/module/content_view_version_info/)
