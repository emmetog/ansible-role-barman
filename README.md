emmetog.ansible-role-barman [![Build Status](https://travis-ci.org/emmetog/ansible-role-barman.svg?branch=master)](https://travis-ci.org/emmetog/ansible-role-barman)
=========

💥 Battle-tested at [Grofers](https://grofers.com/) and [Aptvision](https://aptvision.com).

Ansible role which installs and configures
[barman by 2ndQuadrant](http://www.pgbarman.org/) on debian and RedHat based distros
(Tested with Ubuntu 14.04 and RedHat 7 only, but should work with other similar distros as well).

Installation
------------

This has been tested on Ansible 2.1.0 and higher.
To install:

```bash
ansible-galaxy install emmetog.barman
```

Role Variables
--------------

```yaml
# defaults file for ansible-role-barman
barman_client_only: no

## APT settings
barman_postgresql_apt_key_id: ACCC4CF8
barman_postgresql_apt_key_url: "https://www.postgresql.org/media/keys/ACCC4CF8.asc"
barman_postgresql_apt_repository: "deb http://apt.postgresql.org/pub/repos/apt/ {{ansible_distribution_release}}-pgdg main"
# Pin-Priority of PGDG repository
barman_postgresql_apt_pin_priority: 500

## Cron Configuration
barman_cron_disabled: false
# Run barman cron every minute
barman_cron_schedule:
  minute: "*"
  hour: "*"
  day: "*"
  weekday: "*"
  month: "*"

## Barman Config
barman_user: "barman"
barman_configuration_files_directory: "/etc/barman.d"
barman_home: "/var/lib/barman"
barman_log_directory: "/var/log/barman"
barman_log_file: "{{ barman_log_directory }}/barman.log"
barman_log_level: "INFO"

barman_server_configuration:
  - name: ssh-replication
    active: true
    description: "Example of rsync backup with streaming replication"
    host: "db01.ukcloud.aptvision.com"
    systemdbname: "postgres"

    # Backup options, can be postgres (replication) or rsync
    backup_user: "barman"
    backup_method: rsync
    backup_options: concurrent_backup
    ssh_command: "ssh -q postgres@my-postgres-db"
    reuse_backup: link

    # Streaming WALS, can be streaming (replication) or traditional archiving
    streaming_user: "streaming_barman"
    streaming_archiver: "on"
    slot_name: barman_1
    archiver: off

    # Set this to true to disallow backups to run according to the schedule below
    cron_disabled: false
    cron_schedule:
        minute: "0"
        hour: "0"
        day: "*"

```
There are a lot more optional variables, please see defaults/main.yml for all
of them.

Example Playbook
----------------

```yaml
    - name: Setup and configure barman
      become: yes
      roles:
        - emmetog.barman
```

License
-------

[MIT](LICENSE)

Author Information
------------------

[vishesh92](github.com/vishesh92)
[emmetog](github.com/emmetog)
