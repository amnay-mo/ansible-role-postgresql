[![Ansible Galaxy](https://img.shields.io/badge/role-amnay--mo.postgresql-blue.svg)](https://galaxy.ansible.com/amnay-mo/postgresql/)
### README: install PostgreSQL and configure Streaming Replication

Features:
- based on [postgresql-on-el6](https://galaxy.ansible.com/list#/roles/766) role.
- supported distributions:
  - RedHat, CentOS, Scientific Linux version 6 and 7
  - Oracle Linux also supported but uses RHEL repositories
  - Debian 8
  - Ubuntu 14.04
- supported PostgreSQL versions: 9.0, 9.1, 9.2, 9.3, 9.4, 9.5.
- allows specify users, and dedicated replication user and databases which would be created after install.
- ability to determine a set of postgresql.conf parameters and absense postgresql.conf template. Template is not used due to the fact that the postgresql.conf differs from version to version on a set of parameters.
- ability to specify another cluster directory and setup symlink into original data location.
- ability to specify extension list for created databases (only for versions >= 9.0).

Known issues:
- RHEL 6: packages from pgdg repo does not install due to repo configuration parsing error. Workaround: replace $releasever variable to your specific release version. This issue does not occurs on the others distributions.
- RHEL 6: posqtgresqlXY-contrib doesn't install due to dependency error. In my case, this may be caused by the use of an unregistered RedHat distribution and unofficial third-party repositories. On the Oracle Enterprise Linux this issue does not occur.

How-to use:

- specify `postgresql_master` boolean for master and slaves host or group variable or whatever:
```
[db-postgresql]
192.168.0.10 postgresql_master=true
192.168.0.11 postgresql_master=false
192.168.0.12 postgresql_master=false
```
- specify master and slaves ip addresses and other configuration in your variables:
```
postgresql_streaming_user:
  name: rep
  pass: secret
postgresql_streaming_master: 192.168.0.10
postgresql_streaming_slaves:
 - 192.168.0.11
 - 192.168.0.12
```
- set other relevant variables. (examples in `defaults/main`)
- **important**: `sudo` must be installed on remote machines.
- Run your playbook.
```
---
- name: Install and configure postgresql replica sets
  hosts: db-postgresql
  remote_user: root
  vars_files:
    - 'vars_postgresql.yml'
  roles:
    - { role: ansible-role-postgresql }
```
