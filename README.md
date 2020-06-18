# ansible-kanboard

Ansible role for install and configure [kanboard](https://kanboard.org)

Limitation
----------

1. This role not install `php`, `php extentions` and `nginx` you must first install them yourself
see [additional](README.md#Additional)
2. Web server must have permission for readable and executable in the kanboard dir
3. If you want to use an external DB such as (Mysql/PostgreSQL) install the selected DB before the run role

Requirements
------------
Tested on RHEL 7, CentOS 7/8

* ansible 2.6 or above
* PHP 7.2 or above
* nginx

Role Variables
--------------
**It is not necessary to use all these variable blocks, you can use only the config options you really need.** 

Available variables are be listed below, along with default values. See [defaults/main.yml](defaults/main.yml).

You can change all variables by group_vars or host_vars

| Variable name | Required* | Description | Default Value |
| --- | --- | --- | --- |
| `kanboard_version` | **Yes** | Version of the installing kanboard software | 1.2.14 | 
| `kanboard_user` | **No** | OS user name | kanboard | 
| `kanboard_group` | **No** | OS group name | nginx | 
| `kanboard_secure_install` | **No** | Remove not safety files | true |
| `kanboard_session_duration` | **No** | session duration | 0 |
| `kanboard_security_config_map` | **No** | dictionary of security setting | {} |
| `kanboard_bin_path` | **No** | kanboard binary dir | /opt/kanboard | 
| `kanboard_data_path` | **No** | Kanboard data dir | /opt/kanboard/data | 
| `kanboard_log_path` | **No** | kanboard log dir | /opt/kanboard/logs | 
| `kanboard_plugin_path` | **No**   | kanboard plugin dir | /opt/kanboard/plugins | 
| `kanboard_download_tmp` | **No** | temp dir for download kanboard | /tmp/kanboard | 
| `kanboard_log_driver` | **No** | log driver for kanboard | file |
| `kanboard_logrotate_freq` | **No** | how many days keep logs | 7 |
| `kanboard_backend` | **No** | backend DB for kanboard | sqlite |
| `kanboard_ldap_config_map` | **No** | dictionary of ldap setting | {} |

If your want use Mysql/PostgeSQL for backend you must set additional variables:

| Variable name | Required* | Description | Default Value |
| --- | --- | --- | --- |
| `kanboard_db_host` | **Yes** | DB hostname  | localhost | 
| `kanboard_db_port` | **No** | DB port | depends of DB type| 
| `kanboard_db_username` | **Yes** | DB username | kanboard | 
| `kanboard_db_password` | **Yes** | DB password | ""  | 
| `kanboard_db_name` | **No** | DB name | kanboard | 

Ldap kanboard parameters see on [ldap](https://docs.kanboard.org/en/latest/admin_guide/ldap_authentication.html)

Dictionary `kanboard_ldap_config_map`

| Variable name | Required* | Default Value |
| --- | --- | --- |
| `ldap_server` | **Yes** |  | 
| `ldap_port` | **No** | 389 | 
| `ldap_ssl_verify` | **No** | true | 
| `ldap_start_tls` | **No** |  false  | 
| `ldap_username_case_sensitive` | **No** | false |
| `ldap_bind_type` | **No** | anonymous |
| `ldap_username` | **No** | null |
| `ldap_password` | **No** | null |
| `ldap_user_base_dn` | **Yes** |  |
| `ldap_user_filter` | **Yes** |  | 
| `ldap_user_attribute_username` | **No** | uid |
| `ldap_user_attribute_fullname` | **No** | cn |
| `ldap_user_attribute_email` | **No** | mail |
| `ldap_user_attribute_groups` | **No** | memberOf |
| `ldap_user_attribute_photo` | **No** |  |
| `ldap_user_attribute_language` | **No** |  |
| `ldap_user_creation` | **No** | true |
| `ldap_group_admin_dn` | **Yes**  |   |
| `ldap_group_manager_dn` | **Yes** |  |
| `ldap_group_provider` | **No** | false |
| `ldap_group_base_dn` | **Yes** |  |
| `ldap_group_filter` | **Yes** |  |
| `ldap_group_user_filter` | **Yes** |  |
| `ldap_group_user_attribute` | **No** | username |
| `ldap_group_attribute_name` | **No** | cn |

Security kanboard parameters see on [security](https://docs.kanboard.org/en/latest/admin_guide/security.html)

Dictionary `kanboard_security_config_map`

| Variable name | Required* | Default Value |
| --- | --- | --- |
| `remember_me_auth` | **No** | true |
| `enable_hsts` | **No** | true |
| `enable_xframe` | **No** | true |
| `bruteforce_captcha` | **No** | 3 | 
| `bruteforce_lockdown` | **No** | 6 | 
| `bruteforce_lockdown_duration` | **No** | 15 | 

****Required*** - if "yes", you need to set this parameter under your conditions.

All configuration parameters you can see on the [kanboard documentation](https://docs.kanboard.org/en/latest/admin_guide/config_file.html)

Dependencies
------------
none

Additional
-----------
1. for install nginx you can use this role [nginxinc.nginx] [1]:
2. for install php and extensions you can use this role [geerlingguy.php] [2]:
For example
```yaml
- name: Install php and nginx
  hosts: 
    - kanbord.example.com
  become: yes
  become_method: sudo
  roles:
    - nginxinc.nginx
    - geerlingguy.repo-remi
    - geerlingguy.php-versions
    - geerlingguy.php
  vars:
    php_version: '7.3'
    php_webserver_daemon: "nginx"
    php_packages_state: "latest"
    php_packages:
      - php
      - php-cli
      - php-fpm
      - php-json
      - php-gd
      - php-mbstring
      - php-pgsql
      - php-xml
      - php-ldap
      - php-zip
```
 
Example Playbook
------------

```yml
- name: Install kanboard
  hosts: 
    - kanbord.example.com
  become: yes
  become_method: sudo
  roles:
    - vitcheus.kanboard
  vars:
    kanboard_version: "1.2.13"
    kanboard_user: nginx
    kanboard_backend: postgres
    kanboard_db_host: db.example.com
    kanboard_db_port: 5432
    kanboard_db_name: kanboard
    kanboard_db_username: kanboard
    kanboard_db_password: password
    kanboard_ldap_config_map:
      ldap_server: ldap.example.com
      ldap_bind_type: proxy
      ldap_username: "uid=technical,cn=users,cn=accounts,dc=example,dc=com"
      ldap_password: password
      ldap_user_base_dn: "cn=users,cn=accounts,dc=example,dc=com"
      ldap_user_filter: "(&(objectClass=inetorgperson)(uid=%s)(memberOf=CN=kanboard-users,cn=groups,cn=accounts,dc=example,dc=com))"
      ldap_username_case_sensitive: true
      ldap_group_admin_dn: "cn=kanboard-administrators,cn=groups,cn=accounts,dc=example,dc=com"
      ldap_group_manager_dn: "cn=kanboard-manager,cn=groups,cn=accounts,dc=example,dc=com"
      ldap_group_provider: true
      ldap_group_base_dn: "cn=groups,cn=accounts,dc=example,dc=com"
      ldap_group_filter: "(&(objectClass=groupofnames)(cn=%s*))"
      ldap_group_user_filter: "(&(objectClass=groupofnames)(member=uid=%s,cn=users,cn=accounts,dc=example,dc=com))"
    kanboard_security_config_map:
      remember_me_auth: false
      bruteforce_lockdown: 8
      bruteforce_lockdown_duration: 30
```

[1]: https://github.com/nginxinc/ansible-role-nginx
[2]: https://github.com/geerlingguy/ansible-role-php