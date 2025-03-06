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
> - `size` is required to create/expand a volume.  
> - `vol_id` is required to rename/delete a volume.  
> - `vol_name`, `sg_name`, and `new_sg_name` are required to move volumes between storage groups.  
> - Deletion of a volume will fail if the storage group is part of a masking view.  
> - The modules in this collection, named as `dellemc.powermax`, are built to support the Dell PowerMax storage platform.  
# Task: Create Volume

The Create Volume task ensures that a volume is created in the specified Storage Group on the PowerMax system. It also includes error handling and status reporting.

## Task Breakdown

### Ensure Volume Creation
The task attempts to create a volume using the `dellemc.powermax.volume` module.

### Error Handling (Rescue Block)
If the creation fails, an error message is captured and displayed.

### Status Display (Always Block)
Provides feedback on whether the volume was created successfully, already exists, or failed.

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

The `vmax/volume_role/vars/create_volume.yml` file contains the necessary variables for the Create Volume task. These variables should be customized as per the requirements.

### Variables in `create_volume.yml`

| Variable       | Description                                      |
|---------------|--------------------------------------------------|
| `vol_name`    | Name of the volume to be created.               |
| `volume_size` | Numeric size of the volume.                     |
| `sg_name`     | Name of the Storage Group where the volume will be assigned. |
| `cap_unit`    | Unit of measurement for the volume size (e.g., GB, MB, TB). |
| `append_vol_id` | Whether to append a unique volume ID to the volume name (`true`/`false`). |

## Usage Instructions

1. Define the required variables in `vmax/volume_role/vars/create_volume.yml`.
2. Execute the role using Ansible Playbook.
3. Monitor the output messages to verify success or debug errors.
