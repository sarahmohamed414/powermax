volume_role | Manage volumes on PowerMax Storage System
=========

Managing volumes on PowerMax storage system includes creating a volume, renaming a volume, and deleting a volume.

Requirements
------------

The below requirements are needed on the host that executes this module.

A Dell PowerMax storage system.
Ansible-core 2.15 or later.
Python 3.10, 3.11 or 3.12.

Parameters
--------------

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

Notes
------------

Note

To expand a volume, either provide vol_id or vol_name or vol_wwn and sg_name.
size is required to create/expand a volume.
vol_id is required to rename/delete a volume.
vol_name, sg_name and new_sg_name is required to move volumes between storage groups.
Deletion of volume will fail if the storage group is part of a masking view.
The modules present in this collection named as 'dellemc.powermax' are built to support the Dell PowerMax storage platform.
