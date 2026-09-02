# Red Hat Satellite - Export Content Views

Ansible modules for interacting with the Satellite API exporting content views versions.

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
|`satellite_operation_timeout`|7200|no|integer|Timeout in seconds|2 hours in seconds, integer|
|`satellite_poll_interval`|30|no|integer|Polling in seconds|30 seconds, integer|
|`satellite_max_retries`|3|no|integer|Polling in seconds|3 attempts, integer|
|`satellite_retry_delay`|30|no|integer|Polling in seconds|30 seconds, integer|
|`satellite_export_wait_time`|21600|no|integer|Polling in seconds|21600 seconds == 6 hours|

### Content View Settings
|Variable Name|Default Value|Required|Type|Description|Example|
|:---|:---|:---:|:---:|:---|:---|
|`satellite_timestamp`|"`{{ ansible_date_time['date'] }}`"|yes|string|||
|`satellite_directories`|"/var/lib/pulp"|yes|string|Satellite pulp directory||
|`satellite_export_content_view_version`|`true`|yes|boolean|Exporting Content View Version||
|`satellite_export_lock_file`|"/tmp/satellite-export.lock"|yes|string|Exporting Content View Version||
|`satellite_export_history`|`true`|yes|boolean|Exporting Content View Version||
|`satellite_export_path`|"`/var/lib/pulp/exports/export-{{ timestamp }}`"|yes|string| ||
|`satellite_export_description`|"`Exported via automation on {{ timestamp }}`"|yes|string|Exporting Content View Version||
|`satellite_required_space_gb`|500|yes|integer|Default space required|500 is equal to 500GB|
|`satellite_cleanup_old_exports`|`true`|no|boolean|Optional clean up of old||
|`satellite_cleanup_old_export_age`|"30d"|yes|string|Age of exports to keep|7 days, 7d|
|`satellite_export_content_view_format`|"importable"|yes|string|importable, syncable||
|`satellite_export_destination_server_fqdn`|""|yes|string|Destination server name; optional||
|`satellite_export_content_view_incremental`|`false`|yes|boolean|incremental(true) or complete(false)||
|`satellite_export_content_view_fail_on_missing_content`|`true`|yes|boolean|Fail on missing repositories||
|`satellite_export_content_view_name`|""|yes|string|Content view name|satellite6|
|`satellite_export_content_view_version_number`|""|yes|string|Content view version number|1.0|


#### **`playbook.yml`**  

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
    - name: INCLUDE_ROLE | export_content_views
      ansible.builtin.include_role:
        name: export_content_view
...
```

## Documentation

### Red Hat Satellite Collection

[Link](https://console.redhat.com/ansible/automation-hub/repo/published/redhat/satellite/)

#### Modules
- [redhat.satellite.content_export_version](https://console.redhat.com/ansible/automation-hub/repo/published/redhat/satellite/content/module/content_export_version/)
- [redhat.satellite.content_export_info](https://console.redhat.com/ansible/automation-hub/repo/published/redhat/satellite/content/module/content_export_info/)
