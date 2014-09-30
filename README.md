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

      mariadb_port: 3306                 # The port for mariadb server to listen
      mariadb_bind_address: "0.0.0.0"    # The bind address for mariadb server
      mariadb_root_db_pass: foobar       # The root DB password

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


Dependencies
------------

None

License
-------

BSD

Author Information
------------------

Benno Joy


