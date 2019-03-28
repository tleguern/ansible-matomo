ansible-matomo
==============

Ansible role for configuring Matomo. While it is possible to use it as is it was designed to be used from Packer using the ansible-local provisioner.

This role will first configure mysql and nginx to run a freshly downloaded Matomo 3.9.0. Then it will complete the manual installation process automaticaly thanks to the uri module.

What is **not** automatic yet:

- configuration of the geoip database ;
- configuration of the trusted sites ;
- user creation.

Requirements
------------

For Debian 9 or equivalent:

- python3-openssl
- php
- php-curl
- php-gd
- php-cli
- php-mysql
- php-xml
- php-mbstring
- php-fpm
- python3-mysqldb
- mysql-server
- nginx
- tar

Role Variables
--------------

- *matomo_mysql_database*: Optionnal definition of the database name, default is “matomodata” ;
- *matomo_mysql_user*: Optionnal name for mysql user, default is “matomo” ;
- *matomo_mysql_password*: Password for *matomo_mysql_user* ;
- *matomo_name*: The domain name pointing to matomo ;
- *matomo_superuser_user*: Name for matomo superuser ;
- *matomo_superuser_password*: Password for *matomo_superuser_user* ;
- *matomo_version*: The specific Matomo version to install, a default is provided ;
- *matomo_proxy*: Configure Matomo to use the X-Forwarded-For header ;
- *mysql_rescue_user*: Optionnal rescue mysql user with SUPER and PROCESS rights on *matomo_mysql_database* ;
- *mysql_rescue_password*: Password for *mysql_rescue_user*.

Dependencies
------------

None.

Example Playbook
----------------

    - hosts: matomo
      vars:
        mysql_rescue_user: rescue
        mysql_rescue_password: "{{ vaulted_mysql_rescue_password }}"
        matomo_mysql_password: "{{ vaulted_matomo_mysql_password }}"
        matomo_superuser_user: matomo_admin
        matomo_superuser_password: "{{ vaulted_matomo_superuser_password }}"
        matomo_name: stats.example.org
      roles:
      - role: ansible-matomo

Example with Packer
-------------------

In your JSON file add something like this:

    {
      "type": "ansible-local",
      "playbook_file": "install_matomo.yaml",
      "inventory_groups": "matomo",
      "extra_arguments": [
        "-b"
      ]
    }

You might need to user a shell provider first to install ansible as well as your playground (group_vars, roles and vault keys).

License
-------

ISC

Author Information
------------------

Written by Tristan Le Guern on behalf of Deveryware.
