# ansible-matomo

[![builds.sr.ht status](https://builds.sr.ht/~tleguern/ansible-matomo.svg)](https://builds.sr.ht/~tleguern/ansible-matomo?)

Ansible role for configuring Matomo, formerly known as Piwik.

This role configures Matomo, formerly known as Piwik, and optionally handle the configuration of MySQL and Nginx for minimal installations.
It then download a given release and complete the manual installation process automatically thanks to the [uri](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/uri_module.html) module.

What is **not** automatic yet:

- Configuration of the geoip database ;
- Configuration of the trusted sites ;
- User creation.

Automatic testing is provided using molecule's delegated driver and https://builds.sr.ht.
For now only the `converge` step is implemented.

It should be noted that this role is not idempotent yet.

## Requirements

### Debian 9

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

Nginx is listed as an example, any http server implementing FastCGI will work.

### OpenBSD 6.8

- php
- php-gd
- php-pdo-mysql
- php-curl
- gtar

Matomo can be installed in a chroot and works fine with OpenBSD's httpd.

## Role Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `matomo_mysql_database` | Optional definition of the database name | `matomodata` |
| `matomo_mysql_user` | Optional name for mysql user | `matomo` |
| `matomo_mysql_password` | Password for `matomo_mysql_user` | |
| `matomo_name` | The domain name pointing to matomo | mandatory |
| `matomo_superuser_user` | Name for matomo superuser | mandatory |
| `matomo_superuser_password` | Password for `matomo_superuser_user` | mandatory |
| `matomo_superuser_email` | Email address for `matomo_superuser_user` | mandatory |
| `matomo_version` | The specific matomo version to install | "3.9.0" |
| `matomo_proxy` | Configure matomo to use the X-Forwarded-For header | `no` |
| `mysql_rescue_user` | Optional rescue mysql user with SUPER and PROCESS rights on `matomo_mysql_database`. Only used if `matomo_bypass_mysql` is not `yes`. | `` |
| `mysql_rescue_password` | Password for `mysql_rescue_user`. Only used if `matomo_bypass_mysql` is not `yes`. | `` |
| `matomo_bypass_mysql` | Do not configure mysql | `yes` |
| `matomo_bypass_nginx` | Do not configure nginx | `yes` |
| `matomo_www_directory` | Matomo install directory | `/var/www` |

## Dependencies

Any role configuring MySQL, such as:

- geerlingguy.mysql
- aversiste.mysql

Any role configuring a web server, such as:

- geerlingguy.nginx
- reallyenglish.ansible-role-httpd

A role configuring PHP is a plus.

## Example Playbooks

Minimal installation with included lightweight configuration of Nginx and MySQL on Debian:

```yml
- hosts: matomo
  vars:
    matomo_mysql_password: "{{ vaulted_matomo_mysql_password }}"
    matomo_superuser_user: matomo_admin
    matomo_superuser_password: "{{ vaulted_matomo_superuser_password }}"
    matomo_name: stats.example.org
  roles:
  - role: aversiste.matomo
```

Regular installation on OpenBSD. Additional steps are needed once the installation is over to open the web server to the Internet:

```yml
- hosts: matomo
  vars:
    matomo_mysql_password: matomo
    matomo_superuser_password: adminadmin42
    matomo_version: "3.9.0"
    matomo_name: localhost
    matomo_superuser_email: admin@example.org
    mysql_databases:
      - name: "{{ matomo_mysql_database }}"
    mysql_users:
      - name: "{{ matomo_mysql_user }}"
        password: "{{ matomo_mysql_password }}"
        priv: "*.*:FILE/{{ matomo_mysql_database }}.*:SELECT,INSERT,UPDATE,DELETE,CREATE,DROP,ALTER,CREATE\ TEMPORARY\ TABLES,LOCK\ TABLES"
    mysql_db_admin_password: adminpassword
    httpd_conf_domains:
      - name: matomo
        config: |
          listen on 127.0.0.1 port 80
          directory index index.php
          root "/matomo"
          fastcgi socket "/run/php-fpm.sock"
  pre_tasks:
    - name: Configure pdo_mysql
      file:
        src: "/etc/php-7.3.sample/{{ item }}"
        dest: "/etc/php-7.3/{{ item }}"
        state: link
      loop: ['pdo_mysql.ini', 'gd.ini', 'curl.ini']
    - name: Start php-fpm
      service:
        name: php73_fpm
        state: started
        enabled: yes
  roles:
    - role: aversiste.mysql
    - role: reallyenglish.ansible-role-httpd
    - role: aversiste.matomo
```

## License

ISC

## Contributing

Either send [send GitHub pull requests](https://github.com/Aversiste/ansible-matomo) or [send patches on SourceHut](https://lists.sr.ht/~tleguern/misc).

## Author Information

Written by Tristan Le Guern on behalf of Deveryware.
