<img width="664" alt="image" src="https://github.com/user-attachments/assets/611e431b-d729-45a6-b0e8-f8facd1587ec" />

# OEM_Clone - v1.0.0
Automated, silent Oracle Enterprise Manager (OEM) Cloud Control OMS Clone using Ansible. Supported OEM versions: 13.5

# Suggestions and contributions are most welcome!

# OEM OMS Clone Automation using Ansible

This repository provides a fully automated procedure to clone Oracle Enterprise Manager (OEM) Cloud Control OMS to a new Linux server. With the exception of cloning the repository database and assumes that the database clone has been performed successfully. I plan to add a role for db_duplicate in a future release.
This code is provided "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED.

I have tested this code successfully in a controlled environment, but every organization’s infrastructure, security policies, and software versions can differ hence, please check all the code, understand what it is doing, make necessary modifications, check ansible and system requirements, review and customize the variables, take necessary backups, and test before implementing this in production.

---

## Supported OEM Versions:
- Oracle Enterprise Manager 13.5

---

## High-Level Steps Performed:
### Source OMS
- Check prerequisite
- Copy EMKey to repository
- Prepare OMS binaries on source
- Prepare agent software on source
### OMS Repository clone
- Clone OEM repository database (manual step). OEM13.5 supports both CDB and non-CDB repository database. There can be several approaches to clone the database hence, I left this piece, for now, to be performed based on your preference. **** This step must be executed before proceeding with the next steps.
### Target OMS
- Install Required Packages on Target OEM server
- Install Agent on Target OEM server
- Paste OMS binary from Source to Target OEM server
- Clone OMS software
- Configure OEM plugins
- Start OMS service
- Post-configuration tasks
- Secure EM Key in source OMS

---

## Prerequisites:

- Python 3 and Ansible 2.10+
- Oracle user access on source and target servers
- Manual clone of the repository DB via RMAN
- All binaries: agent ZIP, OMS clone JAR, software library tar file should be prepared and copied beforehand

---

## Variables:

Update the following:
```yaml
source:
  oem_version: 13.5
  oracle_home: /u01/app/oracle/product/19.3.0/dbhome_1
  oms_home: /u01/app/middleware/mware13_5
  agent_base: /refresh1/agent135
  os_user: oracle
  os_group: oinstall
  db_name: oemdb
  repo_conn_str: "(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=dbhost)(PORT=1521))(CONNECT_DATA=(SID=oemdb)))"
  em_domain_name: GCDomain
  msport: 7202
  agent_port: 3872
  upload_port: 4903
  agent_reg_password: mysecretpassword
  sys_password: my_sys_password
  sysman_password: my_sysman_password
  agent_soft_lib_path: /u01/app/middleware/software_lib

target:
  oem_version: 13.5
  oracle_home: /u01/app/oracle/product/19.3.0/dbhome_1
  oms_home: /u01/app/middleware/mware13_5
  agent_base: /u01/app/middleware/agent
  os_user: oracle
  os_group: oinstall
  oms_host_name: newoms.example.com
  db_name: oemdb
  repo_conn_str: "(DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=dbhost)(PORT=1521))(CONNECT_DATA=(SID=oemdb)))"
  em_domain_name: GCNewDomain #Must be different from source
  msport: 7202
  admin_server_https_port: 
  agent_port: 3872
  upload_http_port: 
  upload_https_port: 
  agent_reg_password: mysecretpassword #Should be same as source OMS
  sys_password: my_sys_password #Should be same as source OMS
  sysman_password: my_sysman_password #Should be same as source OMS 
  as_user_password:  #Weblogic password - Can be same or different than source OMS
  node_manager_password:  #Weblogic password - Can be same or different than source OMS
  oms_config_files_path: #Path to OMS Instance Home

oms_clone_dir: /oembackup/oms_clone
platform: "Linux x86-64"
agent_export_dest: /oembackup/oms_clone/agent_export
agent_software_lib: /oembackup/oms_clone/agent_software_lib
```
---
# Running the Playbook
OMS clone is divided into two parts:
- The prepare phase, that is executed on the source OMS server
- The clone phase, that is executed on the target OMS server, and finally an additional role executed on the source OMS server

Running the clone_oms_prepare.yml
```shell
ansible-playbook -i your_inventory_loc/hosts.yml clone_oms_prepare.yml -f 5 -e 'source_server=<Source_OEM_Server>'
```

Running the clone_oms.yml
```shell
ansible-playbook -i your_inventory_loc/hosts.yml clone_oms.yml -f 5 -e 'source_server=<Source_OEM_Server> target_server=<Target_OEM_Server>'
```
---
# Contributing
- If you encounter bugs, missing features, or unexpected behavior, please open an issue or submit a pull request.
- Share your enhancements to help improve the code for community benefit.

Thanks and I hope it helps!
