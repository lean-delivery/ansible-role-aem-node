aem-node role
=========
[![License](https://img.shields.io/badge/license-Apache-green.svg?style=flat)](https://raw.githubusercontent.com/lean-delivery/ansible-role-aem-node/master/LICENSE)
[![Build Status](https://travis-ci.org/lean-delivery/ansible-role-aem-node.svg?branch=master)](https://travis-ci.org/lean-delivery/ansible-role-aem-node)
[![Build Status](https://gitlab.com/lean-delivery/ansible-role-aem-node/badges/master/build.svg)](https://gitlab.com/lean-delivery/ansible-role-aem-node)
[![Galaxy](https://img.shields.io/badge/galaxy-lean__delivery.aem-node-blue.svg)](https://galaxy.ansible.com/lean_delivery/aem-node)
![Ansible](https://img.shields.io/ansible/role/d/role_id.svg)
![Ansible](https://img.shields.io/badge/dynamic/json.svg?label=min_ansible_version&url=https%3A%2F%2Fgalaxy.ansible.com%2Fapi%2Fv1%2Froles%2Frole_id%2F&query=$.min_ansible_version)

Role for Adobe Experience Manager installation and configuration.
This role installs AEM (Authoring and Publishing roles) instance on RHEL/Ubuntu-based systems.

This role:
- installs AEM (6.0, 6.1, 6.2, 6.3 6.4 versions for choosing, 6.4 by default ) and configures it as a systemd Linux service
- can change default admin password
- can install any predefined AEM packages (such as hotfixes\service packs)
- configures replication agent on author for publishing if it is not a standalone environment
- supports multiple publishing instances for one authoring
- can create aem groups with predefined permissions
- can create aem user accounts



Requirements
------------
 - Minimal Version of the ansible for installation: 2.5
 - **Java 8** [![Build Status](https://travis-ci.org/lean-delivery/ansible-role-java.svg?branch=master)](https://travis-ci.org/lean-delivery/ansible-role-java)
 - **Supported OS**:
   - CentOS
     - 7
   - RHEL
     - 7
   - Ubuntu
     - 18.04


Role Variables : default
--------------


AEM version (6.0, 6.1, 6.2, 6.3, 6.4)
- `aem_version`: '6.4'

List of additional AEM Packages to install
- `aem_packages`: []

True if you need "noSampleContent" run mode, or false - if you don't
- `aem_no_sample_content`: False

dispatcher role support
- `web_server_ssl`: False
- `web_server_https_port`: 443
- `web_server_http_port`: 80

Server link to download
- `ftp_server_link`: 'ftp://ftp:ftp@ftp.com/aem/'

AEM type author or publisher
- `aem_instance_type`: "author"

Comma separated custom run modes
- `aem_custom_modes`: ''

Default AEM root path
- `aem_root`: /opt/aem

Default AEM port
- `aem_instance_port`: 4502

list of publishers for replication agents configuration
- `publishers`: []

list of dispatchers for flush agents configuration
- `dispatchers`: []

Default AEM admin user  login password
- `aem_admin_login`: admin
- `aem_admin_password`: admin

Default AEM environment type (dev test uat etc.)
- `environment_type`: dev_test

Linux username and group which operates AEM
- `aem_user`: aem
- `aem_group`: aem
- `aem_user_id`: 99999
- `aem_group_id`: 19999

Do you want to change default admin password?
- `aem_change_default_admin_password`: False

AEM groups which would be created during provision proccess

```yml
 aem_groups:
   -
     # AEM Group authorizableID
     id: 'test_group'
     # AEM Group Name
     name: 'Test group'
     # AEM Group description
     description: 'All test users'
     # AEM Group permissions
     permissions:
       - 'path:/,read:true'
       - 'path:/etc/packages,read:true,modify:true,create:true,delete:false,replicate:true'
     # AEM Group parent group
     root_groups: 
       - 'everyone'
```

- `aem_groups`: []

AEM users which would be created during provision proccess
For CI/CD role Don't forget to create user for Jenkins and add it into 'Administrators' build-in AEM group

```yml
 aem_users:
   -
     # AEM user path in crx
     category: 'test'
     # User ID
     id: 'test_user'
     # AEM Group description
     first_name: 'Test'
     # AEM Group permissions
     second_name: 'User'
     # AEM User password
     password: 'test_user_password'
     # AEM user primary group
     'group' : 'everyone'
```


- `aem_users`: []

AEM systemd configuration: Java garbage collectors "UseG1GC",  UseParallelGC , UseSerialGC

- `aem_garbage_collector`: "UseG1GC"
- `aem_perm_size`: "512m"

Do you need replication configuration for author-> publisher dispatcher? 
- `replication_enabled`: False


Example file repository structure
----------------

Structure for AEM 6.4 and 6.3 :
```
./aem/6.3
./aem/6.4
./aem/6.3/aem.jar
./aem/6.4/aem.jar
./aem/licenses/6.3/license.properties
./aem/licenses/6.4/license.properties
```

Example Inventory
----------------
```
 [aem_authors]
 author.example.com

 [aem_publishers]
 publisher.example.com

```

Example Playbook
----------------


```yml
---

- name: java_install
  hosts: all
  roles:
    - role: lean_delivery.java


- name: author_install
  hosts: aem_authors

  roles:
    - role: ansible-role-aem-node
      replication_enabled: True
      aem_instance_type: author
      ftp_server_link: "{{ lookup('env','STORAGE_AWS') }}/aem"

      aem_groups:
       -
        id: 'test_group'
        name: 'Test'
        description: 'All test users'
        permissions:
          - 'path:/,read:true'
          - 'path:/etc/packages,read:true,modify:true,create:true,delete:false,replicate:true'
        root_groups:
          - 'everyone'
      aem_users:
       -
        category: 'test'
        id: 'test_user'
        first_name: 'Test'
        second_name: 'User'
        password: 'test_user_password'
        group: 'test_group'

```


License
-------
Apache

Author Information
------------------

authors:
  - Lean Delivery Team <team@lean-delivery.com>
