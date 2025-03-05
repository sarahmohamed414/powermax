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

vol_name (optional, str, None)
The name of the volume.
sg_name (optional, str, None)
The name of the storage group.
new_sg_name (optional, str, None)
The name of the target storage group.
vol_id (optional, str, None)
The native id of the volume.

Required for rename and delete volume operations.

size (optional, float, None)
The new size of existing volume.

Required for create and expand volume operations.

cap_unit (optional, str, None)
volume capacity units.

If not specified, default value is GB.

new_name (optional, str, None)
The new volume identifier for the volume.
vol_wwn (optional, str, None)
The WWN of the volume.
append_vol_id (optional, bool, None)
Appends volume id to the volume name, Applicable from V4 (Juniper).
state (True, str, None)
Defines whether the volume should exist or not.
unispherehost (True, str, None)
IP or FQDN of the Unisphere host
universion (False, int, None)
Unisphere version. This parameter has been deprecated. It is no longer necessary to specify this parameter.
verifycert (True, str, None)
Specifies system whether to validate SSL certificate or not, Values can be True or False or a custom file path for SSL certificate with .pem extension or .cer with base 64 encoding.
user (True, str, None)
The username of the Unisphere host.
password (True, str, None)
The password of the Unisphere host.
timeout (optional, int, 120)
Time after which the connection will get terminated.

It is to be mentioned in seconds.

port (optional, int, 8443)
The port of the Unisphere host.
serial_no (True, str, None)
The serial number of the PowerMax/VMAX array. It is a required parameter for all array-specific operations except for getting a list of arrays in the Gatherfacts module.


Notes
------------

Note

To expand a volume, either provide vol_id or vol_name or vol_wwn and sg_name.
size is required to create/expand a volume.
vol_id is required to rename/delete a volume.
vol_name, sg_name and new_sg_name is required to move volumes between storage groups.
Deletion of volume will fail if the storage group is part of a masking view.
The modules present in this collection named as 'dellemc.powermax' are built to support the Dell PowerMax storage platform.
