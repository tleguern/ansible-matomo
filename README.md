ansible-matomo
==============

[![builds.sr.ht status](https://builds.sr.ht/~tleguern/ansible-matomo.svg)](https://builds.sr.ht/~tleguern/ansible-matomo?)

Ansible role for configuring Matomo, formerly known as Piwik.

This role configures Matomo, formerly known as Piwik, and optionally handle the configuration of MySQL and Nginx for minimal installations.
It then download a given release and complete the manual installation process automatically thanks to the [uri](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/uri_module.html) module.

What is **not** automatic yet:

- Configuration of the geoip database ;
- Configuration of the trusted sites ;
- User creation.

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

| Variable | Description | Default |
|----------|-------------|---------|
| `matomo_mysql_database` | Optional definition of the database name | `matomodata` |
| `matomo_mysql_user` | Optional name for mysql user | `matomo` |
| `matomo_mysql_password` | Password for `matomo_mysql_user` | |
| `matomo_name` | The domain name pointing to matomo | mandatory |
| `matomo_superuser_user` | Name for matomo superuser | mandatory |
| `matomo_superuser_password` | Password for `matomo_superuser_user` | mandatory |
| `matomo_version` | The specific matomo version to install | "3.9.0" |
| `matomo_proxy` | Configure matomo to use the X-Forwarded-For header | `no` |
| `mysql_rescue_user` | Optional rescue mysql user with SUPER and PROCESS rights on `matomo_mysql_database`. Only used if `matomo_bypass_mysql` is not `yes`. | `` |
| `mysql_rescue_password` | Password for `mysql_rescue_user`. Only used if `matomo_bypass_mysql` is not `yes`. | `` |

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

You might need to use a shell provider first to install ansible as well as your playground (group_vars, roles and vault keys).

License
-------

ISC

Author Information
------------------

Written by Tristan Le Guern on behalf of Deveryware.
