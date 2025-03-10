# Dell PowerMax Ansible Modules  

The Ansible modules for **Dell Technologies (Dell) PowerMax** enable data center and IT administrators to automate and orchestrate the configuration and management of **Dell PowerMax arrays** using **Red Hat Ansible**.  

## Features  

These Ansible modules support:  
- **Volume management**  
- **Storage group management**  

## Requirements  

Before using these modules, ensure you have the following:  

- **A Dell PowerMax storage system**  
- **Ansible-core 2.15 or later**  
- **Python 3.10, 3.11, or 3.12**  
- **PyU4V (Python SDK for Dell PowerMax API)**  

---

## Installing and Using Ansible Modules for Dell PowerMax  

### Installing the SDK  

The **PyU4V** SDK version depends on your **PowerMax Unisphere** version:  

- **For PowerMax Unisphere version 92**, install:  
  ```bash
  pip install PyU4V==9.2.1.6
  ```
- **For PowerMax Unisphere version 100 and above**, install:  
  ```bash
  pip install PyU4V==10.0.0.16
  ```  
  *(If using a virtual environment, activate it before running the command.)*

### Installing Collections  

To install the latest collection hosted on Ansible Galaxy, use:  
```bash
ansible-galaxy collection install dellemc.powermax -p <install_path>
```

### Using Collections  

To use an Ansible module, ensure the proper **Fully Qualified Collection Name (FQCN)** is included in your playbook. Example:  
```yaml
collections:
  - dellemc.powermax
```

### Configuration  

After installing the dependencies, update the default configuration files for `volume_role` and `storage_group_role` to define connection settings for PowerMax.  

#### Example: `volume_role/defaults/main.yml`  
```yaml
unispherehost: "10.118.128.103"  # IP address of the PowerMax system
verifycert: false
serial_no: '000297901660'
ansible_python_interpreter: /root/pyu4v_env/bin/python3
universion: 92
```

#### Example: `storage_group_role/defaults/main.yml`  
```yaml
unispherehost: "10.118.128.103"  # IP address of the PowerMax system
verifycert: false
serial_no: '000297901660'
ansible_python_interpreter: /root/pyu4v_env/bin/python3
universion: 92
append_vol_id: true
```

Make sure to update these variables to match your environment before running the playbooks.
