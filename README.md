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
- installs AEM (6.3 6.4 6.5 versions for choosing, 6.4 by default ) and configures it as a systemd Linux service
- can add AEM Service Pack during installation, zip file with SP will be placed in the crx-quickstart/install directory
- can change default admin password
- can install any predefined AEM packages (such as hotfixes\service packs)
- configures replication agent on author for publishing if it is not a standalone environment
- supports multiple publishing instances for one authoring
- can create aem groups with predefined permissions
- can create aem user accounts
- can configure File Data Store when using a NAS to store shared file data stores
- can install and configure Amazon S3 and Azure Data Stores for binary data. The following combinations have been tested and work:
  - AEM 6.4 with Amazon S3 connector v1.8.6
  - AEM 6.5 with Amazon S3 connector v1.8.6
  - AEM 6.5 with Azure blob connector v1.9.12
- can install Jolokia agent to monitor server resources using


Requirements
------------
 - Minimal Version of the ansible for installation: 2.7
 - **Java 8** [![Build Status](https://travis-ci.org/lean-delivery/ansible-role-java.svg?branch=master)](https://travis-ci.org/lean-delivery/ansible-role-java) for AEM 6.3, 6.4
 - **Java 11** for AEM 6.5
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

- `aem_version` - Version of AEM node wich to install (6.3, 6.4, 6.5)\
  default: `6.4`
- `aem_packages` - List of additional AEM Packages to install\
  default: `[]`
- `dispatcher_ssl` - configure flush agent on ssl dispatcher port\
  default: `False`
- `dispatcher_https_port` - Https port for listening\
  default: `443`
- `dispatcher_http_port` - Http port for listening\
  default: `80`
- `aem_instance_type` - AEM type (author or publish)\
  default: `author`
- `aem_custom_modes` - Comma separated custom run modes wich allow you to tune your AEM instance for a specific purpose; for example author or publish, test, development, intranet or others\
  default: ``
- `aem_root` - Default AEM root path\
  default: `/opt/aem`
- `aem_instance_port` - Default AEM port\
  default: `4502`
- `publishers` - List of publishers for replication agents configuration\
  default: `[]`
- `publisher_ssl` - Enable ssl for replication agents\
  default: `False` 
- `dispatchers` - List of dispatchers for flush agents configuration\
  default: `[]`
- `dispatcher_ssl` - Enable ssl for flush agents\
  default: `False`
- `dispatcher_https_port` - ssl port for flush agents\
  default: `443`
- `dispatcher_http_port` - http port for flush agents\
  default: `80`
- `aem_ssl_enable` - Enable and configure  ssl on aem node\
  default: `False`
- `aem_instance_ssl_port` - Set AEM node ssl port\
  default: `8443`
- `aem_ssl_dir:` - crt and key dir for AEM ssl configuration\
  default: `'{{aem_root}}/ssl'` 
- `aem_ssl_hostname` - hostname for AEM ssl configuration\
  default: `localhost`
- `ssl_key_full_path` - full path to the ssl file\
  default: `'{{ aem_ssl_dir }}/{{ aem_ssl_hostname }}.key'`
- `ssl_crt_full_path`- full path to the ssl file\
  default: `'{{ aem_ssl_dir }}/{{ aem_ssl_hostname }}.crt'`
- `ssl_der_full_path` - full path to the ssl file\
  default: `'{{ aem_ssl_dir }}/{{ aem_ssl_hostname }}.der'`
- `aem_admin_login` - Default AEM admin user login\
  default: `admin`
- `aem_admin_password` - Default AEM admin user password\
  default: `admin`
- `environment_type` - AEM environment type (dev test uat etc.)\
  default: `dev_test`
- `aem_user` - Linux username which operates AEM\
  default: `aem`
- `aem_group` - Linux usergroup which operates AEM\
  default: `aem`
- `aem_user_id` - Linux user's uid\
  default: `99999`
- `aem_group_id` - Linux usergoup's gid\
  default: `19999`
- `aem_change_default_admin_password` - Change or not default admin password\
  default: `False`
  
**Transport configuration:**  
- `download_transport` - one of: web, s3, azure\
  default: `web`
  
  _WEB transport specific:_
- `web_transport_common_url` - Server link to download installation packages\
    default: `ftp://ftp:ftp@ftp.com/aem`
- `full_aem_web_transport_link` - link for aem installation file\
  default: `{{ web_transport_common_url }}/{{ aem_version }}/aem.jar`
- `full_license_web_transport_link` - link for aem license file\
  default: `{{ web_transport_common_url }}/licenses/{{ aem_version }}/license.properties`
- `full_sp_web_transport_link` - link for AEM Service Pack zip file. Zip file with SP will be placed in the crx-quickstart/install directory before first run\
  default: `''` use relative to `web_transport_common_url` path
  
  _AWS S3 transport specific:_
- `transport_s3_bucket` - s3 bucket\
  default: `aemartifacts`
- `aem_transport_s3_path` - aem installation file s3 path\
  default: `/{{ aem_version }}/aem.jar`
- `license_transport_s3_path` - aem license file s3 path\
  default: `/licenses/{{ aem_version }}/license.properties`
- `service_pack_s3_path` - link for AEM Service Pack zip file. Zip file with SP will be placed in the crx-quickstart/install directory before first run\
  default: `''` use relative to `transport_s3_bucket` path
- `transport_s3_aws_access_key` - access key\
  default: `{{ lookup('env','AWS_ACCESS_KEY_ID') }}`
- `transport_s3_aws_secret_key` - secret key\
  default: `{{ lookup('env','AWS_SECRET_ACCESS_KEY') }}`
  
  _Azure blob container transport specific:_
- `transport_azure_resource_group` - name of Azure resource group
- `transport_azure_storage_account_name` - name of Azure storage account
- `transport_azure_container` - name of blob container
- `aem_transport_azure_path` - aem installation file Azure blob container path\
  default: `/{{ aem_version }}/aem.jar`
- `license_transport_azure_path` - aem license file Azure blob container path\
   default: `/licenses/{{ aem_version }}/license.properties`
- `service_pack_azure_path` - link for AEM Service Pack zip file. Zip file with SP will be placed in the crx-quickstart/install directory before first run\
   default: `''` use relative to `transport_azure_container` path
- `transport_azure_subscription_id` - Azure subscription ID\
   default: `{{ lookup('env','AZURE_SUBSCRIPTION_ID') }}`
- `transport_azure_tenant_id` - Azure tenant ID\
   default: `{{ lookup('env','AZURE_TENANT') }}`
- `transport_azure_client_id` - Azure client ID\
   default: `{{ lookup('env','AZURE_CLIENT_ID') }}`
- `transport_azure_client_secret` - Azure client secret\
   default: `{{ lookup('env','AZURE_SECRET') }}`

**Data stores configuration:**
- `datastore_type` - enable custom Data Stores\
  default: `""`\
  Available options:
  - `file` This is the implementation of FileDataStore present in Jackrabbit 2. It provides a way to store the binary data as normal files on the file system. Use it to override default configuration
  - `s3` Amazon S3 Data Store
  - `azure` Azure Data Store

  _File Data Store specific:_\
When using a NAS to store shared file data stores. Use it to override default configuration. **WARNING! Make sure the directory exists, check directory permissions**
- `file_datastore_repository_home` - The path on the file system where repository data will be stored. Set to override default property value
- `file_datastore_path` - The path to the directory under which the files would be stored. If specified then it takes precedence over `file_datastore_repository_home` value
  
  _Amazon S3 Data Store specific:_
- `adobe_repo_feature_pack_link` - Link to download feature pack with Amazon S3 Data Store Connector zip file\
  sample: `https://repo.adobe.com/nexus/content/groups/public/com/adobe/granite/com.adobe.granite.oak.s3connector/1.8.6/com.adobe.granite.oak.s3connector-1.8.6.zip`
- `adobe_repo_feature_pack_md5_link` - Link to download feature pack's md5 file. Can be redefined if needed
  default: `{{ adobe_repo_feature_pack_link }}.md5`\
  Replace with proper http(s) URL if needed
- `s3_data_store_bucket` - The name of S3 bucket where binary data will be stored
- `s3_data_store_region` - The S3 bucket region
- `s3_data_store_acces_key` - The AWS access key
- `s3_data_store_secret_key` - The AWS secret access key
- `s3_data_store_iam_role` - Alternatively, IAM roles can be used for authentication. If you are using IAM roles you no longer need to specify the accessKey and secretKey\
  default: `False`  

  _Azure Data Store specific:_
- `adobe_repo_feature_pack_link` - Link to download feature pack with Amazon S3 Data Store Connector zip file\
  sample: `https://repo.adobe.com/nexus/content/groups/public/com/adobe/granite/com.adobe.granite.oak.azureblobconnector/1.9.12/com.adobe.granite.oak.azureblobconnector-1.9.12.zip`
- `adobe_repo_feature_pack_md5_link` - Link to download feature pack's md5 file. Can be redefined if needed
  default: `{{ adobe_repo_feature_pack_link }}.md5`\
  Replace with proper http(s) URL if needed
- `azure_data_store_access_key` - The Azure storage account name
- `azure_data_store_blob_endpoint` - The Azure Storage blob endpoint\
  default: `https://{{ azure_data_store_access_key }}.blob.core.windows.net`
- `azure_data_store_secret_key` - The storage access key. Ensure that the '=' character is escaped like '\\='
- `azure_data_store_container` - The Microsoft Azure blob storage container name. **WARNING! The name of blob container can't be longer than 18 characters (not documented anywhere!)**
- `azure_data_store_sas` - In version 1.6.3 of the connector, Azure Shared Access Signature (SAS) support was added. If both SAS and storage credentials exists in the configuration file, SAS has priority.  Ensure that the '=' character is escaped like '\\='

**Jolokia agent configuration:**
- `jolokia_agent` - Insert javaagent in author node\
  default: `False`
- `jolokia_agent_port_author` - Port for jolokia agent\
  default: `9998`
- `jolokia_agent_port_publisher` - Port for jolokia agent\
  default: `9999`

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

- `aem_groups`\
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


- `aem_users`\
  default: `[]`

### AEM systemd configuration: Java garbage collectors "UseG1GC",  UseParallelGC , UseSerialGC

- `aem_garbage_collector`\
  default: `UseG1GC`
- `aem_perm_size`\
  default: `512m`
- `Xmx_size` -  specifies the maximum memory allocation pool for a Java virtual machine (JVM)\
  default: `'{{ (ansible_memtotal_mb * 0.7) |int|abs }}'`
- `Xms_size` - specifies the initial memory allocation pool.\
  default: `'{{ (ansible_memtotal_mb * 0.7) |int|abs }}'`

Your JVM will be started with Xms amount of memory and will be able to use a maximum of Xmx amount of memory. 
For example, starting a JVM like below will start it with 256 MB of memory and will allow the process to use up to 2048 MB of memory
with: 
`Xms_size: 256`
`Xmx_size: 2048`


- `replication_enabled` - Enable replication configuration for author-> publisher dispatcher\
  default: `False`



Example installation file repository structure
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
Don't forget to preinstall LDI AEM modules.

```yml
---

- name: java_install
  hosts: all
  roles:
    - role: lean_delivery.java
      transport: repositories
      java_major_version: 8

- name: publisher_install
  hosts: aem_publishers
  roles:
    - role: ansible-role-aem-node
      replication_enabled: true
      aem_instance_type: publish
      web_transport_common_url: ftp://ftp:ftp@aem.example.com/aem
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
        password: 'Test_user_password1'
        group: 'test_group'


- name: author_install
  hosts: aem_authors

  roles:
    - role: ansible-role-aem-node
      replication_enabled: true
      aem_instance_type: author
      web_transport_common_url: ftp://ftp:ftp@aem.example.com/aem
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
        password: 'Test_user_password1'
        group: 'test_group'

- name: author-install-s3-transport-plus-sp
  hosts: aem_authors

  roles:
    - role: ansible-role-aem-node
      aem_version: '6.5'
      download_transport: s3
      transport_s3_bucket: aem_artifacts
      aem_transport_s3_path: /6.5/aem.jar
      license_transport_s3_path: /license/6.5/license.properties
      service_pack_s3_path: /sp/6.5/AEM-6.5.3.0-6.5.3.zip
      transport_s3_aws_access_key: AAABBBCCC
      transport_s3_aws_secret_key: AAABBBCCCDDDEEE
      replication_enabled: true
      aem_instance_type: author
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
        password: 'Test_user_password1'
        group: 'test_group'

- name: author-install-s3-data-store
  hosts: aem_authors

  roles:
    - role: ansible-role-aem-node
      aem_version: '6.5'
      aem_instance_type: author
      ...
      datastore_type: s3
      adobe_repo_feature_pack_link: https://repo.adobe.com/nexus/content/.../com.adobe.granite.oak.s3connector-1.8.6.zip
      s3_data_store_bucket: my-some-data-store
      s3_data_store_region: us-east-1
      s3_data_store_acces_key: AAABBBCCC
      s3_data_store_secret_key: AAABBBCCCDDDEEE

- name: author-install-azure-data-store
  hosts: aem_authors

  roles:
    - role: ansible-role-aem-node
      aem_version: '6.5'
      aem_instance_type: author
      ...
      datastore_type: azure
      adobe_repo_feature_pack_link: https://repo.adobe.com/nexus/content/.../com.adobe.granite.oak.azureblobconnector-1.9.12.zip
      azure_data_store_access_key: my-some-storage-account
      azure_data_store_secret_key: "VHnh83bXJmMgzL...Oyp28sffOgAq0VGrHU6ScwpA\=\="
      azure_data_store_container: my-aem-container

- name: author-install-custom-file-data-store
  hosts: aem_authors

  roles:
    - role: ansible-role-aem-node
      aem_version: '6.5'
      aem_instance_type: author
      ...
      datastore_type: file
      file_datastore_path: /mnt/nas/datastore
```


License
-------
Apache

Author Information
------------------

authors:
  - Lean Delivery Team <team@lean-delivery.com>
