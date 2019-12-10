aem-node role
=========
[![License](https://img.shields.io/badge/license-Apache-green.svg?style=flat)](https://raw.githubusercontent.com/lean-delivery/ansible-role-aem-node/master/LICENSE)
[![Build Status](https://travis-ci.org/lean-delivery/ansible-role-aem-node.svg?branch=master)](https://travis-ci.org/lean-delivery/ansible-role-aem-node)
[![Build Status](https://gitlab.com/lean-delivery/ansible-role-aem-node/badges/master/pipeline.svg)](https://gitlab.com/lean-delivery/ansible-role-aem-node)
[![Galaxy](https://img.shields.io/badge/galaxy-lean__delivery.aem_node-blue.svg)](https://galaxy.ansible.com/lean_delivery/aem_node)
![Ansible](https://img.shields.io/ansible/role/d/39641.svg)
![Ansible](https://img.shields.io/badge/dynamic/json.svg?label=min_ansible_version&url=https%3A%2F%2Fgalaxy.ansible.com%2Fapi%2Fv1%2Froles%2F39641%2F&query=$.min_ansible_version)

Role for Adobe Experience Manager installation and configuration.
This role installs AEM (Authoring and Publishing roles) instance on RHEL/Ubuntu-based systems.

This role:
- installs AEM (6.3 6.4 versions for choosing, 6.4 by default ) and configures it as a systemd Linux service
- can change default admin password
- can install any predefined AEM packages (such as hotfixes\service packs)
- configures replication agent on author for publishing if it is not a standalone environment
- supports multiple publishing instances for one authoring
- can create aem groups with predefined permissions
- can create aem user accounts


Requirements
------------
 - Minimal Version of the ansible for installation: 2.5
 - Please 
 - **Java 8** [![Build Status](https://travis-ci.org/lean-delivery/ansible-role-java.svg?branch=master)](https://travis-ci.org/lean-delivery/ansible-role-java)
 - Ansible Modules:
    - LDI AEM modules https://github.com/lean-delivery/ansible-modules-aem
 - **Supported OS**:
   - CentOS
     - 7
   - RHEL
     - 7
   - Ubuntu
     - 16.04
     - 18.04


Role Variables : default
--------------

- `aem_version` - Version of AEM node wich to install (6.3, 6.4)  
  default: `6.4`

- `aem_packages` - List of additional AEM Packages to install
  default: `[]`

- `dispatcher_ssl` - configure flush agent on ssl dispatcher port  
  default: `False`
- `dispatcher_https_port` - Https port for listening  
  default: `443`
- `dispatcher_http_port` - Http port for litening  
  default: `80`

- `web_transport_common_url` - Server link to download installation packages  
  defult: `ftp://ftp:ftp@ftp.com/aem/`

- `aem_instance_type` - AEM type (author or publisher)  
  default: `author`

- `aem_custom_modes` - Comma separated custom run modes wich allow you to tune your AEM instance for a specific purpose; for example author or publish, test, development, intranet or others  
  default: ``

- `aem_root` - Default AEM root path  
  default: `/opt/aem`

- `aem_instance_port` - Default AEM port  
  default: `4502`

- `publishers` - List of publishers for replication agents configuration  
  default: `[]`
- `publisher_ssl` -  Enable ssl for replication agents
  default: `False` 

- `dispatchers` - List of dispatchers for flush agents configuration  
  default: `[]`
- `dispatcher_ssl` - Enable ssl for flush agents
  default: `False`
- `dispatcher_https_port` - Ssl port for flush agents
  default: `443`
- `dispatcher_http_port` - http port for flush agents
  default: `80`

- `aem_ssl_enable` - Enable and configure  SSl on aem node
  default: `False`
- `aem_instance_ssl_port` Set AEM node ssl port
  default: `8443`
- `aem_ssl_dir:`  - crt and key dir for AEM ssl configuration
  default: `'{{aem_root}}/ssl'` 
- `aem_ssl_hostname` hostname for AEM ssl configuration
  default: `localhost`
- `ssl_key_full_path` - full path to the ssl file
  default: `'{{ aem_ssl_dir }}/{{ aem_ssl_hostname }}.key'`
- `ssl_crt_full_path`- full path to the ssl file
  default: `'{{ aem_ssl_dir }}/{{ aem_ssl_hostname }}.crt'`
- `ssl_der_full_path` - full path to the ssl file
  default: `'{{ aem_ssl_dir }}/{{ aem_ssl_hostname }}.der'`
- `aem_admin_login` - Default AEM admin user login  
  default: `admin`
- `aem_admin_password` - Default AEM admin user password  
  default: `admin`

- `environment_type` - AEM environment type (dev test uat etc.)  
  default: `dev_test`

- `aem_user` - Linux username which operates AEM  
  default: `aem`
- `aem_group` - Linux usergroup which operates AEM  
  default: `aem`
- `aem_user_id` - Linux user's uid  
  default: `99999`
- `aem_group_id` - Linux usergoup's gid  
  default: `19999`

- `aem_change_default_admin_password` - Change or not default admin password  
  default: `False`

### AEM groups which would be created during provision proccess

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

- `aem_groups`
  default: `[]`

### AEM users which would be created during provision proccess
## For CI/CD role Don't forget to create user for Jenkins and add it into 'Administrators' build-in AEM group

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


- `aem_users`
  default: `[]`

### AEM systemd configuration: Java garbage collectors "UseG1GC",  UseParallelGC , UseSerialGC

- `aem_garbage_collector`   
  default: `UseG1GC`
- `aem_perm_size`   
  default: `512m`

- `replication_enabled` - Enable replication configuration for author-> publisher dispatcher   
  default: `False`


Example file repository structure
----------------

Structure for AEM 6.4 and 6.3 :
```yml
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
 
 [publisher_dispatchers]
 dispatcher1.example.com
 dispatcher2.example.com

```

Example Playbook
----------------


```yml
---

- name: java_install
  hosts: all
  roles:
    - role: lean_delivery.java

- name: publisher_install
  hosts: aem_publisher
  roles:
    - role: ansible-role-aem-node
      replication_enabled: true
      aem_instance_type: publisher
      web_transport_common_url: "{{ lookup('env','STORAGE_AWS') }}/aem"
      dispatchers: "{{ groups['publisher_dispatchers'] }}"
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


- name: author_install
  hosts: aem_authors

  roles:
    - role: ansible-role-aem-node
      replication_enabled: true
      aem_instance_type: author
      web_transport_common_url: "{{ lookup('env','STORAGE_AWS') }}/aem"
      publishers: "{{ groups['aem_publishers'] }}"
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
