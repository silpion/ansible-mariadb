# 2.1.2


* Use lookup instead of local template vor dump variables
* Use original license (BSD)
* Update Ansible Galaxy info
* Add documentation for mariadb_db parameters

Alvaro Aleman (4):
# 2.1.1


* fix warnings; update style-guide
* fix deprication warnings

Anja Siek (2):
# 2.1.0


* Rewrite README.md
* Remove defaulting for user passwords
* Allow defining databases as string

Alvaro Aleman (3):
# 2.0.1

Alvaro Aleman (4):

* Make role Ansible2 compatible
* Make backup tasks include conditional
* Use become inside role
* Rename service handler to fit style guide

# 2.0.0

This release forgot to integrate changes described below.
2.0.1 really integrates them.

Alvaro Aleman (4):

* Make role Ansible2 compatible
* Make backup tasks include conditional
* Use become inside role
* Rename service handler to fit style guide

# 1.3.1

* Add envvars for centos/7 vagrant box
* Update TDD functionaltiy from ansible-generator
* Use host-specific prefix for local dump vars file

Alvaro Aleman (3):

# 1.3.0

Alvaro Aleman (14):

* Update Readme
* Rework dump tasks file
* Fix backupser creation
* Add dumpvars to gitignore since it gets locally created
* Dynamically create dumpvars using local_action: template
* Add mariadb_backupjobs.backupuserpass directive
* Add more tests
* Fix backupscript template
* Disable autodeletion of backups by default
* Remove obsolete default filters
* Add always_run: true to local template task
* Fix templating
* Fix codingstyle, add task name for dump include
* Update releasescript

Ruslan Tumarkin (2):

* Add no_log and fix mariadb_users_create if empty on create users task
* Removed when check from create db users task

# 1.1.0

Alvaro Aleman (4):

* mariadb\_backupscript: initial version
* Added tdd infrastructure to role
* [mariadb-backupscript]: Various minor fixes, permissions for backup user reduced, test playbook adjusted
* Removed mariadb\_dump\_destinatordir\_create var, typo fixed, style adjusted

Mark Kusch (1):

* Pick missing commits from earlier release

# 1.0.0

Alvaro Aleman (1):

* CentOS 7 compatibility added

# 0.2.3

Anja Siek (1):

* add possebility to notify users and permission management

Mark Kusch (1):

* Add some minor typo thingis

# 0.2.2

Anja Siek (1):

* add mysql_user value option append_privs

# 0.2.1

Anja Siek (1):

* fix cofiguration

# 0.2.0

Mark Kusch (1):

* s/mysql/mariadb/


<!-- vim: set nofen ts=4 sw=4 et: -->
