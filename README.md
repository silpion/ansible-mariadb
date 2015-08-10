mariadb Server
============

This roles helps to install mariadb Server across RHEL and Ubuntu variants.
Apart from installing the mariadb Server, it applies basic hardening, like
securing the root account with password, and removing test databases. The role
can also be used to add databases to the mariadb server and create users in the
database. It also supports configuring the databases for replication--both
master and slave can be configured via this role.

Requirements
------------

This role requires Ansible 1.4 or higher, and platform requirements are listed
in the metadata file.

Role Variables
--------------

The variables that can be passed to this role and a brief description about
them are as follows:

      mariadb_root_db_pass: foobar       # The root DB password, **mandatory**
      mariadb_port: 3306                 # The port for mariadb server to listen
      mariadb_bind_address: "0.0.0.0"    # The bind address for mariadb server, default: 127.0.0.1

      # A list that has all the databases to be
      # created and their replication status:
      mariadb_db:
           - name: foo
             replicate: yes
           - name: bar
             replicate: no

      # A list of the mariadb users to be created
      # and their password and privileges:
      mariadb_users:
           - name: benz
             pass: foobar
             priv: "*.*:ALL"

      # If the database is replicated the users
      # to be used for replication:
      mariadb_repl_user:
        - name: repl
          pass: foobar

      # The role of this server in replication:
      mariadb_repl_role: master

      # A unique id for the mariadb server (used in replication):
      mariadb_db_id: 7

# MariaDB dump

* mariadb_enable_dump: Wether to execute MariaDB dump tasks (boolean, default: false)
* mariadb_dump_username: Name for the dump user (String, default: mariadb_dump)
* mariadb_dump_userpassword: Password for the dump user (String, **mandatory**)
* mariadb_dump_destinationdir: The directory to dump to (String, **mandatory**)
* mariadb_dump_databases: A list of the databases to dump (List, **mandatory**)
* mariadb_dump_crontime.hour: Hour at which to run the dump cronjob (String, refer to cron manual for details, default: '4')
* mariadb_dump_crontime.dayofweek: Day of week at which to run the dump cronjob (String, refer to cron manual for details, default: '*')
* mariadb_dump_scriptfile: File to store the backupscript in (String, default: '/usr/local/sbin/mariadb_dump')

Examples
--------

1) Install mariadb Server and set the root password, but don't create any
database or users.

      - hosts: all
        roles:
        - {role: mariadb, mariadb_root_db_pass: foobar, mariadb_db: none, mariadb_users: none }

2) Install mariadb Server and create 2 databases and 2 users.

      - hosts: all
        roles:
         - {role: mariadb, mariadb_db: [{name: benz},
                                    {name: benz2}],
            mariadb_users: [{name: ben3, pass: foobar, priv: "*.*:ALL"},
                          {name: ben2, pass: foo}] }

Note: If users are specified and password/privileges are not specified, then
default values are set.

3) Install mariadb Server and create 2 databases and 2 users and configure the
database as replication master with one database configured for replication.

      - hosts: all
        roles:
         - {role: mariadb, mariadb_db: [{name: benz, replicate: yes },
                                    { name: benz2, replicate: no}],
                         mariadb_users: [{name: ben3, pass: foobar, priv: "*.*:ALL"},
                                       {name: ben2, pass: foo}],
                         mariadb_repl_user: [{name: repl, pass: foobar}] }

4) A fully installed/configured mariadb Server with master and slave
replication.

      - hosts: master
        roles:
         - {role: mariadb, mariadb_db: [{name: benz}, {name: benz2}],
                         mariadb_users: [{name: ben3, pass: foobar, priv: "*.*:ALL"},
                                       {name: ben2, pass: foo}],
                         mariadb_db_id: 8 }

      - hosts: slave
        roles:
         - {role: mariadb, mariadb_db: none, mariadb_users: none,
                  mariadb_repl_role: slave, mariadb_repl_master: vm2,
                  mariadb_db_id: 9, mariadb_repl_user: [{name: repl, pass: foobar}] }

Note: When configuring the full replication please make sure the master is
configured via this role and the master is available in inventory and facts
have been gathered for master. The replication tasks assume the database is
new and has no data.

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




License
-------

BSD

Author Information
------------------

Benno Joy

<!-- vim: set nofen ts=4 sw=4 et: -->
