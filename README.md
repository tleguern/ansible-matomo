ansible-matomo
==============

Ansible role for configuring Matomo. While it is possible to use it as is it was designed to be used from Packer using the ansible-local provisioner.

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

Role Variables
--------------

- *matomo_mysql_database*: Optionnal definition of the database name, default is “matomodata” ;
- *matomo_mysql_user*: Optionnal name for mysql user, default is “matomo” ;
- *matomo_mysql_password*: Password for *matomo_mysql_user* ;
- *matomo_name*: The domain name pointing to matomo ;
- *matomo_superuser_user*: Name for matomo superuser ;
- *matomo_superuser_password*: Password for *matomo_superuser_user* ;
- *mysql_rescue_user*: Optionnal rescue mysql user with SUPER and PROCESS rights on *matomo_mysql_database* ;
- *mysql_rescue_password*: Password for *mysql_rescue_user*.

Dependencies
------------

None.

Example Playbook
----------------

    - hosts: all
      vars:
        mysql_rescue_user: rescue
        mysql_rescue_password: "{{ vaulted_mysql_rescue_password }}"
        matomo_mysql_password: "{{ vaulted_matomo_mysql_password }}"
        matomo_superuser_user: matomo_admin
        matomo_superuser_password: "{{ vaulted_matomo_superuser_password }}"
        matomo_name: stats.example.org
      roles:
      - role: ansible-matomo

License
-------

ISC

Author Information
------------------

Written by Tristan Le Guern on behalf of Deveryware.
