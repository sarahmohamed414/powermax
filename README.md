# Dell PowerMax Ansible Modules  

The Ansible modules for **Dell Technologies (Dell) PowerMax** allow data center and IT administrators to use **RedHat Ansible** to automate and orchestrate the configuration and management of **Dell PowerMax arrays**.  

## Features  
The capabilities of these Ansible modules include:  
- Managing **volumes**  
- Managing **storage groups**  

## Requirements  

To use this project, ensure you have the following:  

- **A Dell PowerMax storage system**  
- **Ansible-core 2.15 or later**  
- **Python 3.10, 3.11, or 3.12**  
- **PyU4V (Python SDK for Dell PowerMax API)**  

### Installation  

Install `PyU4V` using:  

```bash
pip install PyU4V
If using a virtual environment, activate it before running the command.)

Configuration
After installing the dependencies, update the defaults files for volume_role and storage_group_role to define connection settings for PowerMax.

Example variables in volume_role/defaults/main.yml
unispherehost: "10.118.128.103"  # IP address of the PowerMax system
verifycert: false
serial_no: '000297901660'
ansible_python_interpreter: /root/pyu4v_env/bin/python3
universion: 92


Example variables in storage_group_role/defaults/main.yml
unispherehost: "10.118.128.103"  # IP address of the PowerMax system
verifycert: false
serial_no: '000297901660'
ansible_python_interpreter: /root/pyu4v_env/bin/python3
universion: 92
append_vol_id: true


Make sure to update these variables to match your environment before running the playbooks.

