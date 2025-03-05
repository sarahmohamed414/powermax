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

- **sg_name** *(True, str, None)*  
  The name of the storage group.  

- **service_level** *(optional, str, None)*  
  The name of SLO.  

- **srp** *(optional, str, None)*  
  The name of the storage resource pool.  
  - This parameter is ignored if `service_level` is not specified.  
  - Default is to use whichever is the default SRP on the array.  

- **compression** *(optional, bool, None)*  
  Compression on the storage group.  
  - This parameter is ignored if `service_level` is not specified.  
  - Default is `true`.  

- **volumes** *(optional, list, None)*  
  This is a list of volumes.  
  - Each volume has four attributes: `vol_name`, `size`, `cap_unit`, `vol_id`.  
  - `vol_id` must be provided for existing volumes.  
  - `size` must be provided to add new volumes to SG.  
  - `cap_unit` is optional. Default is `GB` (Choices: `MB`, `GB`, `TB`).  

- **vol_state** *(optional, str, None)*  
  Describes the state of volumes inside the storage group.  

- **child_storage_groups** *(optional, list, None)*  
  A list of child storage groups.  

- **child_sg_state** *(optional, str, None)*  
  Describes the state of child storage groups inside the parent SG.  

- **new_sg_name** *(optional, str, None)*  
  The new name of the storage group.  

- **target_sg_name** *(optional, str, None)*  
  The destination storage group name to move the volumes to.  

- **force** *(optional, bool, None)*  
  This flag must be set to `True` while moving volumes to the target SG if the volume is in a masking view.  

- **snapshot_policies** *(optional, list, None)*  
  A list of snapshot policies.  

- **snapshot_policy_state** *(optional, str, None)*  
  Describes the state of the snapshot policy for a storage group.  

- **host_io_limit** *(optional, dict, None)*  
  Host I/O limit of the storage group.  

  - **host_io_limit_iops** *(optional, int, None)*  
    The I/Os per second host I/O limit for the storage group.  

  - **dynamic_distribution** *(optional, str, Never)*  
    The dynamic distribution of host I/O limit for the storage group.  

  - **host_io_limit_mbps** *(optional, int, None)*  
    The MBs per second host I/O limit for the storage group.  

- **state** *(True, str, None)*  
  Defines whether the storage group should exist or not.  

- **unispherehost** *(True, str, None)*  
  IP or FQDN of the Unisphere host.  

- **universion** *(False, int, None)*  
  Unisphere version. *(Deprecated: No longer necessary to specify this parameter.)*  

- **verifycert** *(True, str, None)*  
  Specifies whether to validate the SSL certificate.  
  - Accepts `True`, `False`, or a custom file path for SSL certificate (`.pem` or `.cer`).  

- **user** *(True, str, None)*  
  The username of the Unisphere host.  

- **password** *(True, str, None)*  
  The password of the Unisphere host.  

- **timeout** *(optional, int, 120)*  
  The time (in seconds) after which the connection will be terminated.  

- **port** *(optional, int, 8443)*  
  The port of the Unisphere host.  

- **serial_no** *(True, str, None)*  
  The serial number of the PowerMax/VMAX array.  
  - Required for all array-specific operations, except for retrieving a list of arrays in the Gatherfacts module.  

## Notes  

- To set **host_io_limit_mbps** to `NOLIMIT`, provide the value as `0`.  
- Idempotency is **not supported** when creating a new volume in the storage group **without providing a volume name**.  
- The **check_mode** is not supported.  
- The modules in this collection, named **`dellemc.powermax`**, are built to support the **Dell PowerMax storage platform**.  
