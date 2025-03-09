Storage_group_role | Manage storage groups on PowerMax or VMAX Storage System
=========

Managing storage groups on a **PowerMax storage system** includes the following:  

1. Create a storage group  
2. Create new volumes, then add them to an existing Storage Group  
3. Add existing volumes to an existing Storage Group  
4. List volumes of a Storage Group  
5. Modify a Storage Group  
6. Delete a Storage Group  
Requirements
------------

The following requirements must be met on the host executing this module:  

- A **Dell PowerMax** storage system  
- **Ansible-core** 2.15 or later  
- **Python** 3.10, 3.11, or 3.12  

Parameters  
--------------
| Parameter                | Required | Type  | Default | Description |
|--------------------------|----------|------|---------|-------------|
| `sg_name`               | Yes      | str  | None    | The name of the storage group. |
| `service_level`         | No       | str  | None    | The name of SLO. |
| `srp`                   | No       | str  | None    | The name of the storage resource pool. Ignored if `service_level` is not specified. Default is the array's default SRP. |
| `compression`           | No       | bool | None    | Compression on the storage group. Ignored if `service_level` is not specified. Default is `true`. |
| `volumes`               | No       | list | None    | A list of volumes. Each volume has attributes: `vol_name`, `size`, `cap_unit`, `vol_id`. `vol_id` is required for existing volumes. `size` is required for new volumes. `cap_unit` defaults to `GB` (Choices: MB, GB, TB). |
| `vol_state`             | No       | str  | None    | Describes the state of volumes inside the storage group. |
| `child_storage_groups`  | No       | list | None    | A list of child storage groups. |
| `child_sg_state`        | No       | str  | None    | Describes the state of child storage groups inside the parent SG. |
| `new_sg_name`          | No       | str  | None    | The new name of the storage group. |
| `target_sg_name`       | No       | str  | None    | The destination storage group name to move the volumes to. |
| `force`                | No       | bool | None    | Set to `True` when moving volumes to `target_sg_name` if they are in a masking view. |
| `snapshot_policies`    | No       | list | None    | A list of snapshot policies. |
| `snapshot_policy_state` | No       | str  | None    | Describes the state of the snapshot policy for a storage group. |
| `host_io_limit`        | No       | dict | None    | Host I/O limit of the storage group. |
| `host_io_limit_iops`   | No       | int  | None    | The I/Os per second host I/O limit for the storage group. |
| `dynamic_distribution` | No       | str  | Never   | The dynamic distribution of host I/O limit for the storage group. |
| `host_io_limit_mbps`   | No       | int  | None    | The MBs per second host I/O limit for the storage group. |
| `state`               | Yes      | str  | None    | Defines whether the storage group should exist or not. |
| `unispherehost`       | Yes      | str  | None    | IP or FQDN of the Unisphere host. |
| `universion`         | No       | int  | None    | Unisphere version (Deprecated, no longer necessary). |
| `verifycert`         | Yes      | str  | None    | Specifies whether to validate the SSL certificate. Accepts `True`, `False`, or a file path (.pem/.cer). |
| `user`               | Yes      | str  | None    | The username of the Unisphere host. |
| `password`           | Yes      | str  | None    | The password of the Unisphere host. |
| `timeout`           | No       | int  | 120     | The time (in seconds) before the connection is terminated. |
| `port`              | No       | int  | 8443    | The port of the Unisphere host. |
| `serial_no`         | Yes      | str  | None    | The serial number of the PowerMax/VMAX array. Required for all array-specific operations except retrieving arrays in the Gatherfacts module. |


## Notes  

- To set **host_io_limit_mbps** to `NOLIMIT`, provide the value as `0`.  
- Idempotency is **not supported** when creating a new volume in the storage group **without providing a volume name**.  
- The **check_mode** is not supported.  
- The modules in this collection, named **`dellemc.powermax`**, are built to support the **Dell PowerMax storage platform**.

# Create a storage group 

## Task Overview
The **Create a storage group ** task ensures that a Storage Group is created in the PowerMax system with the specified Service Level and Storage Resource Pool (SRP). The task includes error handling and status reporting to ensure smooth execution.

## Task Breakdown
### Creating a Storage Group
- Utilizes the `dellemc.powermax.storagegroup` module to create a new Storage Group.
- Assigns the specified **Service Level**, **SRP**, and enables **compression**.

### Error Handling (Rescue Block)
- If the creation fails, an error message is captured and displayed.

### Status Display (Always Block)
- Provides feedback on whether the Storage Group was successfully created, already exists, or encountered an issue.

## Usage Instructions
1. Define the required variables in `powerstorage_group_role/vars/create_sg.yml`.
2. Execute the role using the `storage_management.yml` Ansible playbook.
3. Monitor the output messages to verify success or debug errors.

## Variables
The `power/storagegroup_role/vars/create_storagegroup.yml` file contains the necessary variables for the **Create Storage Group** task. These variables should be customized as per the requirements.

### Variables in `power/storagegroup_role/vars/create_storagegroup.yml`

| Variable      | Description                                      |
|--------------|--------------------------------------------------|
| `sg_name`    | Name of the Storage Group to be created.        |
| `service_level` | Service Level for the Storage Group (e.g., Diamond, Platinum). |
| `srp`        | Name of the Storage Resource Pool (SRP).        |
| `compression` | Whether compression is enabled (true/false).   |

### Example `vars/create_storagegroup.yml`
```yaml
sg_name: "SG_test"  # Enter the Storage Group name
service_level: "Diamond"  # Enter the Service Level
srp: "SRP_1"  # Enter the SRP name
compression: true  # Enable compression (true/false)
```

## Validation Criteria
To ensure the Storage Group is created correctly, verify the following:
- **Service Level (`service_level`)**: Should be one of the predefined service levels (e.g., Diamond, Platinum, Gold).
- **SRP (`srp`)**: Must be an existing and valid Storage Resource Pool.
- **Compression (`compression`)**: Should be set to `true` or `false` as required.

## Code Example
```yaml
- name: Create a new Storage Group with error handling
  block:
    - name: Create a new Storage Group
      dellemc.powermax.storagegroup:
        unispherehost: "{{ unispherehost }}"
        universion: "{{ universion }}"
        verifycert: "{{ verifycert }}"
        user: "{{ user }}"
        password: "{{ password }}"
        serial_no: "{{ serial_no }}"
        sg_name: "{{ sg_name }}"
        service_level: "{{ service_level }}"
        srp: "{{ srp }}"
        compression: true
        state: "present"
      register: sg_result

    - name: Show the result of Storage Group creation
      ansible.builtin.debug:
        var: sg_result

    - name: Show success message
      ansible.builtin.debug:
        msg: "Successfully created Storage Group '{{ sg_name }}' with Service Level '{{ service_level }}'."
      when: sg_result is succeeded and sg_result.changed | default(false)

    - name: Show no change message
      ansible.builtin.debug:
        msg: "Storage Group '{{ sg_name }}' already exists with the specified configuration."
      when: sg_result is succeeded and not sg_result.changed | default(true)

  rescue:
    - name: Show failure message
      ansible.builtin.debug:
        msg: "Failed to create Storage Group '{{ sg_name }}'. Check logs for details."

  always:
    - name: Ensure the task completes cleanly
      ansible.builtin.debug:
        msg: "Storage Group creation process completed."
```

## Example Output
```
TASK [storage_group_role : Show the result of Storage Group creation] ********************************************************
task path: /root/ansible-powermax/storage_group_role/tasks/create_sg.yml:18
ok: [localhost] => {
    "sg_result": {
        "changed": true,
        "create_sg": true,
        "failed": false,
        "storage_group_details": {
            "service_level": "Diamond",
            "srp": "SRP_1",
            "storageGroupId": "SG_test",
            "compression": true
        }
    }
}

TASK [storage_group_role : Show success message] **************************************************************************
task path: /root/ansible-powermax/storage_group_role/tasks/create_sg.yml:22
ok: [localhost] => {
    "msg": "Successfully created Storage Group 'SG_test' with Service
```
# Create New Volumes, Then Add Them to an Existing Storage Group

## Task Overview
The **Create New Volumes for Existing Storage Group** task ensures that a new volume is created and added to a specified storage group on the PowerMax system. The process includes error handling and status reporting.

## Task Breakdown
### Volume Creation
This task creates a new volume and assigns it to an existing storage group using the `dellemc.powermax.storagegroup` module.

### Status Reporting
- If the volume is successfully created and added, a confirmation message is displayed.
- If the operation fails, an error message is captured and displayed.

## Usage Instructions
1. Define the required variables in `storage_group_role/vars/create_volumes_for_sg.yml`.
2. Execute the role using the `storage_management.yml` Ansible playbook.
3. Monitor the output messages to verify success or troubleshoot errors.

## Variables
The `powermax/storage_group_role/vars/create_volumes_for_sg.yml` file contains the necessary variables for the task. These variables should be customized as per requirements.

### Variables in `powermax/storage_group_role/vars/create_volumes_for_sg.yml`

| Variable  | Description  |
|-----------|--------------|
| `sg_name`  | Name of the existing storage group where the new volume will be created.  |
| `vol_name`  | Name of the volume to be created.  |
| `size`  | Numeric size of the volume in GB.  |

## Validation Criteria
To ensure successful execution, verify the following:
- The `sg_name` variable points to an existing Storage Group in the PowerMax system.
- The `size` variable is a valid integer value greater than zero.
