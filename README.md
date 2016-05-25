# ansible-mariadb

## Description

This role helps to install and configure the mariadb server and applies
basic hardening to it. The role can also be used to configure database
backups and/or replication.

## Requirements

This role is tested on the platforms listed below but may work on others:

* EL 7
* Ubuntu 14


## Role Variables

A list of available variables. Optional variables are shown with their default values.

### Global

```yml
mariadb_root_db_pass: foobar             # Mandatory: Root db password
mariadb_port: 3306                       # Optional
mariadb_bind_address: 127.0.0.1          # Optional
mariadb_confd_dir: platform specific     # Optional, used for mariadb_config. For default see vars/{{ ansible_os_family }}.yml
mariadb_confd_file: platform specific    # Optional, used for mariadb_config. For default see vars/{{ ansible_os_family }}.yml

mariadb_db:                              # Optional, default: []
  - foo                                  # The name can be either passed as string or via the name key as shown below
  - state: present                       # Optional, default: present
    name: bar
    replicate: false                     # Optional, only has an effect if replication is configured
    collation: utf8_bin                  # Optional, default: omit
    encoding: utf8                       # Optional, default: omit
    target: /tmp/dumpfile.sql            # Optional, default: omit
    login_host: localhost                # Optional, should not be used, default: localhost
    login_port: 3306                     # Optional, default: {{ mariadb_port }}
    login_user: root                     # Optional, default: root
    login_password: 123                  # Optional, default: {{ mariadb_root_db_pass }}
    login_unix_socket: /run/mysql.sock   # Optional, should not be used, default: omit

mariadb_users:                           # Optional
  - name: benz                           # Mandatory
    pass: foobar                         # Mandatory
    priv: "*.*:ALL"                      # Optional
    host: 'localhost'                    # Optional
    append_privs: false                  # Optional, whether to append privileges

mariadb_config:                          # Optional, ini file for custom settings. Default: []
  - section: mysection
    option: myoption
    value: myvalue
```

### Backup/Dump

```yml
mariadb_backupjobs:                      # Optional, default: []
  - name: all_dbs                        # Mandatory, must be no longer than 13 chars, else the role will fail on creating the users
    backupuserpass: 'pw'                 # Mandatory
    databases: 'all'                     # Optional: The databases to dump. This variable must either be a list of databases or the keyword 'all'
    dayofweek: '*'                       # Optional: The weekday(s) to dump at. Use cron notation here
    hour: '4'                            # Optional: The hour to execute job at. Use cron notation here
    destinationdir: '/backup'            # Optional: The directory to put the backups in. Filename is $year-$month-$day-$hour-$minute-$backupjobname.sql.gz
    autodeleteafter: false               # Optional: Amount of days after which backups will be deleted. False means backups never get deleted
mariadb_backup_scriptbasepath: /usr/local/sbin # Optional: Folder to store the backupscripts in
mariadb_backup_defaultdestinationdir: /backup  # Optional: Folder to store backups that don't have a destionationdir attribute in
```


### Replication

```yml
mariadb_repl_role: None                  # Optional, the replication role. Must be in ['none', 'master', 'slave']
mariadb_repl_master: undefined           # Mandatory when using replication. Inventory_hostname of the replication master
mariadb_db_id: 0                         # Mandatory when using replication. Must be unique in replication compound
mariadb_repl_user:                       # Mandatory when mariadb_repl_role: master
  - name: repl
    pass: foobar
```

Note: When configuring the full replication please make sure the master is
configured via this role and the master is available in inventory and facts
have been gathered for master. The replication tasks assume the database is
new and has no data.

## Example playbook

```yml
- hosts: all
  vars:
    mariadb_root_db_pass: foobar
    mariadb_users:
      - name: foo
        pass: bar
        priv: 'seafile_ccnet.*:ALL/seafile_seafile.*:ALL'
    mariadb_db:
      - seafile_ccnet
      - seafile_seafile
    mariadb_backupjobs:
      - name: all_dbs
        backupuserpass: foobar
  roles:
    - ansible-mariadb
```

## Integration testing

This role provides integration tests using the Ruby RSpec/serverspec framework
with a few drawbacks at the time of writing this documentation.

Running integration tests requires a number of dependencies being
installed. As this role uses Ruby RSpec there is the need to have
Ruby with rake and bundler available.

    # install role specific dependencies with bundler
    bundle install

<!-- -->

    # run the complete test suite with Docker
    rake suite

<!-- -->

    # run the complete test suite with Vagrant
    source  envvars-vagrant.sample
    rake suite

    # run the complete test suite with Vagrant without destroying the box afterwards
    source  envvars-vagrant.sample
    RAKE_ANSIBLE_VAGRANT_DONT_CLEANUP=1 rake suite

## Contributing

If you want to contribute to this repository please be aware that this
project uses a [gitflow](http://nvie.com/posts/a-successful-git-branching-model/)
workflow with the next release branch called ``next``.

Please fork this repository and create a local branch split off of the ``next``
branch and create pull requests back to the origin ``next`` branch.

## License

BSD

## Author Information

* Benno Joy
* Mark Kusch @mark.kusch silpion.de
* Alvaro Aleman @alvaro.aleman silpion.de

<!-- vim: set nofen ts=4 sw=4 et: -->
