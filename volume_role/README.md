volume_role | Manage volumes on PowerMax Storage System
=========

## Managing Volumes

Managing volumes on a **PowerMax storage system** includes:  
- Creating a volume  
- Renaming a volume  
- Deleting a volume  


## Requirements

The following requirements must be met on the host executing this module:

- A **Dell PowerMax** storage system  
- **Ansible-core** 2.15 or later  
- **Python** 3.10, 3.11, or 3.12  




## Parameters

| Parameter       | Required | Type    | Default | Description |
|----------------|----------|---------|---------|-------------|
| `vol_name`     | No       | string  | `None`  | The name of the volume. |
| `sg_name`      | No       | string  | `None`  | The name of the storage group. |
| `new_sg_name`  | No       | string  | `None`  | The name of the target storage group. |
| `vol_id`       | No       | string  | `None`  | The native ID of the volume. **Required for rename and delete volume operations.** |
| `size`         | No       | float   | `None`  | The new size of an existing volume. **Required for create and expand volume operations.** |
| `cap_unit`     | No       | string  | `GB`    | Volume capacity units. Default is `GB`. |
| `new_name`     | No       | string  | `None`  | The new volume identifier for the volume. |
| `vol_wwn`      | No       | string  | `None`  | The WWN of the volume. |
| `append_vol_id`| No       | boolean | `None`  | Appends volume ID to the volume name. Applicable from V4 (Juniper). |
| `state`        | Yes      | string  | `None`  | Defines whether the volume should exist or not. |
| `unispherehost`| Yes      | string  | `None`  | IP or FQDN of the Unisphere host. |
| `universion`   | No       | int     | `None`  | Unisphere version. **Deprecated.** No longer necessary to specify. |
| `verifycert`   | Yes      | string  | `None`  | Specifies whether to validate SSL certificates. Accepts `True`, `False`, or a file path (`.pem` or `.cer`). |
| `user`         | Yes      | string  | `None`  | The username of the Unisphere host. |
| `password`     | Yes      | string  | `None`  | The password of the Unisphere host. |
| `timeout`      | No       | int     | `120`   | Time (in seconds) after which the connection will terminate. |
| `port`         | No       | int     | `8443`  | The port of the Unisphere host. |
| `serial_no`    | Yes      | string  | `None`  | The serial number of the PowerMax/VMAX array. **Required for all array-specific operations except listing arrays.** |



> **Note**  
> - To expand a volume, either provide `vol_id`, `vol_name`, or `vol_wwn` along with `sg_name`.  
> - `size` is required to create/expand a volume and it should be a positive integer..  
> - `vol_id` is required to rename/delete a volume.  
> - Deletion of a volume will fail if the storage group is part of a masking view.  
> - The modules in this collection, named as `dellemc.powermax`, are built to support the Dell PowerMax storage platform.
> - The specified Storage Group (sg_name) must exist before attempting to create the volume.
> - cap_unit should be one of the valid values (MB, GB, TB).
# Task: Create Volume

The Create Volume task ensures that a volume is created in the specified Storage Group on the PowerMax system. It also includes error handling and status reporting.

## Task Breakdown

### Ensure Volume Creation
The task attempts to create a volume using the `dellemc.powermax.volume` module.

### Error Handling (Rescue Block)
If the creation fails, an error message is captured and displayed.

### Status Display (Always Block)
Provides feedback on whether the volume was created successfully, already exists, or failed.

## Usage Instructions

1. Define the required variables in `power/volume_role/vars/create_volume.yml`.
2. Execute the role using the volume_management.yml ansible playbook.
3. Monitor the output messages to verify success or debug errors.

## Code Example

```yaml
- name: Ensure volume is created and handle errors
  block:
    - name: Create volume
      register: result
      dellemc.powermax.volume:
        unispherehost: "{{ unispherehost }}"
        universion: "{{ universion }}"
        verifycert: "{{ verifycert }}"
        user: "{{ user }}"
        password: "{{ password }}"
        serial_no: "{{ serial_no }}"
        vol_name: "{{ vol_name }}"
        sg_name: "{{ sg_name }}"
        size: 1
        cap_unit: "{{ cap_unit }}"
        append_vol_id: "{{ append_vol_id }}"
        state: "present"

  rescue:
    - name: Capture failure reason
      ansible.builtin.debug:
        msg: "Failed to create volume: {{ result.msg }}"

  always:
    - name: Display volume creation status
      ansible.builtin.debug:
        msg: >-
          {% if result.changed %}
          "Volume '{{ vol_name }}' was created successfully in Storage Group '{{ sg_name }}'."
          {% elif result.failed is defined and result.failed %}
          "Volume creation failed: {{ result.msg }}"
          {% else %}
          "Volume '{{ vol_name }}' already exists in Storage Group '{{ sg_name }}'."
          {% endif %}
```

## Variables

The `powermax/volume_role/vars/create_volume.yml` file contains the necessary variables for the Create Volume task. These variables should be customized as per the requirements.

### Variables in `powermax/volume_role/vars/create_volume.yml`

| Variable       | Description                                      |
|---------------|--------------------------------------------------|
| `vol_name`    | Name of the volume to be created.               |
| `volume_size` | Numeric size of the volume.                     |
| `sg_name`     | Name of the Storage Group where the volume will be assigned. |
| `cap_unit`    | Unit of measurement for the volume size (e.g., GB, MB, TB). |
| `append_vol_id` | Whether to append a unique volume ID to the volume name (`true`/`false`). |


### vars/create_volume.yml Example
```yaml
vol_name: "Test_volume" # Please enter the name of the volume to be created.
volume_size: 1 # Please enter the size of the volume (numeric value).
sg_name: "Edward_LNK_SG_001" # Please enter the name of the storage group where the volume will be assigned.
cap_unit: 'GB' # Please enter the unit of measurement for the volume size.
append_vol_id: true # Whether to append a unique volume ID to the volume name (true/false).
```

---

## Example Output
```bash
included: /root/ansible-powermax/volume_role/tasks/create_volume.yml for localhost
Read vars_file 'vault.yml'

TASK [volume_role : Create volume] **********************************************************************************************************************************
task path: /root/ansible-powermax/volume_role/tasks/create_volume.yml:3
<127.0.0.1> ESTABLISH LOCAL CONNECTION FOR USER: root
<127.0.0.1> EXEC /bin/sh -c 'echo ~root && sleep 0'
<127.0.0.1> EXEC /bin/sh -c '( umask 77 && mkdir -p "` echo /root/.ansible/tmp `"&& mkdir "` echo /root/.ansible/tmp/ansible-tmp-1741257185.5820274-4054-233184259503660 `" && echo ansible-tmp-1741257185.5820274-4054-233184259503660="` echo /root/.ansible/tmp/ansible-tmp-1741257185.5820274-4054-233184259503660 `" ) && sleep 0'
Using module file /root/.ansible/collections/ansible_collections/dellemc/powermax/plugins/modules/volume.py
<127.0.0.1> PUT /root/.ansible/tmp/ansible-local-4013toqanfiy/tmpe3d7uar5 TO /root/.ansible/tmp/ansible-tmp-1741257185.5820274-4054-233184259503660/AnsiballZ_volume.py
<127.0.0.1> EXEC /bin/sh -c 'chmod u+x /root/.ansible/tmp/ansible-tmp-1741257185.5820274-4054-233184259503660/ /root/.ansible/tmp/ansible-tmp-1741257185.5820274-4054-233184259503660/AnsiballZ_volume.py && sleep 0'
<127.0.0.1> EXEC /bin/sh -c '/root/pyu4v_env/bin/python3 /root/.ansible/tmp/ansible-tmp-1741257185.5820274-4054-233184259503660/AnsiballZ_volume.py && sleep 0'
<127.0.0.1> EXEC /bin/sh -c 'rm -f -r /root/.ansible/tmp/ansible-tmp-1741257185.5820274-4054-233184259503660/ > /dev/null 2>&1 && sleep 0'
changed: [localhost] => {
    "changed": true,
    "invocation": {
        "module_args": {
            "append_vol_id": true,
            "cap_unit": "GB",
            "new_name": null,
            "new_sg_name": null,
            "password": "VALUE_SPECIFIED_IN_NO_LOG_PARAMETER",
            "port": 8443,
            "serial_no": "000297901660",
            "sg_name": "Edward_LNK_SG_001",
            "size": 1.0,
            "state": "present",
            "timeout": 120,
            "unispherehost": "VALUE_SPECIFIED_IN_NO_LOG_PARAMETER",
            "universion": 92,
            "user": "VALUE_SPECIFIED_IN_NO_LOG_PARAMETER",
            "verifycert": "False",
            "vol_id": null,
            "vol_name": "Test_volume",
            "vol_wwn": null
        }
    },
    "volume_details": {
        "allocated_percent": 0,
        "cap_cyl": 547,
        "cap_gb": 1.0,
        "cap_mb": 1026.0,
        "effective_wwn": "60000970000297901660533030303644",
        "emulation": "FBA",
        "encapsulated": false,
        "has_effective_wwn": false,
        "mobility_id_enabled": false,
        "num_of_front_end_paths": 0,
        "num_of_storage_groups": 1,
        "pinned": false,
        "reserved": false,
        "snapvx_source": false,
        "snapvx_target": false,
        "ssid": "FFFFFFFF",
        "status": "Ready",
        "storageGroupId": [
            "Edward_LNK_SG_001"
        ],
        "storage_groups": [
            {
                "storage_group_name": "Edward_LNK_SG_001"
            }
        ],
        "type": "TDEV",
        "unreducible_data_gb": 0.0,
        "volumeId": "0006D",
        "volume_identifier": "Test_volume",
        "wwn": "60000970000297901660533030303644"
    }
}
Read vars_file 'vault.yml'

TASK [volume_role : Display volume creation status] *****************************************************************************************************************
task path: /root/ansible-powermax/volume_role/tasks/create_volume.yml:25
ok: [localhost] => {
    "msg": " \"Volume 'Test_volume' was created successfully in Storage Group 'Edward_LNK_SG_001'.\" "
}
Read vars_file 'vault.yml'

TASK [volume_role : Include Rename volume] **************************************************************************************************************************
task path: /root/ansible-powermax/volume_role/tasks/main.yml:18
skipping: [localhost] => {
    "changed": false,
    "false_condition": "user_input == \"2\"",
    "skip_reason": "Conditional result was False"
}
Read vars_file 'vault.yml'

TASK [volume_role : Include Delete volume] **************************************************************************************************************************
task path: /root/ansible-powermax/volume_role/tasks/main.yml:22
skipping: [localhost] => {
    "changed": false,
    "false_condition": "user_input == \"3\"",
    "skip_reason": "Conditional result was False"
}
Read vars_file 'vault.yml'

PLAY RECAP **********************************************************************************************************************************************************
localhost                  : ok=5    changed=1    unreachable=0    failed=0    skipped=2    rescued=0    ignored=0  
   
