
# PostgreSQL 9 Server (postgresql9-server)

Master: [![Build Status](https://semaphoreci.com/api/v1/bas-ansible-roles-collection/postgresql9-server/branches/master/badge.svg)](https://semaphoreci.com/bas-ansible-roles-collection/postgresql9-server)
Develop: [![Build Status](https://semaphoreci.com/api/v1/bas-ansible-roles-collection/postgresql9-server/branches/develop/badge.svg)](https://semaphoreci.com/bas-ansible-roles-collection/postgresql9-server)

Installs and configures PostgreSQL server

**Part of the BAS Ansible Role Collection (BARC)**

**This role uses version 0.1.0 of the BARC flavour of the BAS Base Project - Pristine**.

## Overview

* Installs PostgreSQL server package from system-only sources
* Installs PostgreSQL contrib package used for additional database extensions from system-only sources
* Installs PostgreSQL Python bindings to allow Ansible to manage database resources (databases/users/etc.)
* Optionally, configures the system firewall to allow access to PostgreSQL services, this is enabled by default
* Configures main PostgreSQL configuration file, for allowing remote connections
* Generates client authentication configuration file, for allowing remote connections

## Quality Assurance

This role uses manual and automated testing to ensure its features work as advertised.
See [here](tests/README.md) for more information.

## Dependencies

* None

More information on role dependencies is available in the
[BARC General Documentation](https://antarctica.hackpad.com/BARC-Overview-and-Policies-SzcHzHvitkt#:h=Role-dependencies)

## Requirements

* None

More information on role requirements is available in the
[BARC General Documentation](https://antarctica.hackpad.com/BARC-Overview-and-Policies-SzcHzHvitkt#:h=Role-requirements)

## Limitations

* Some variables in this role cannot be easily overridden

This specifically affects: *postgresql9_server_system_only_postgres_version*, *postgresql9_server_configuration_base*
and *postgresql9_server_configuration_directory*.

Values for these variables vary on each supported operating system and therefore cannot be defined as variables in 
`defaults/main.yml` (which are universal). Ansible does not support conditionally importing additional variables at 
the same priority of role 'defaults' (i.e. variables defined in `defaults/main.yml`), therefore these variables must be 
set in `vars/` within this role, and conditionally loaded using the 
[include_vars module](http://docs.ansible.com/ansible/include_vars_module.html).

Variables set at this priority cannot be easily overridden in playbooks (i.e. using the `vars` option), or in variable 
files (i.e. using the `vars_files` option). In fact only 'extra_vars' set on the command line can override variables of
this precedence.

Given the nature of these variables, it is not expected likely users will need (or want) to changes the values for these
variables, and therefore the difficultly needed to override them is considered an acceptable, and not significant 
limitation. However, if other variables need to be defined in this way this may need to revisited in the future.

*This limitation is **NOT** considered to be significant. Solutions will **NOT** be actively pursued.*
*Pull requests to address this will be considered.*

See the 
[Ansible Documentation](http://docs.ansible.com/ansible/playbooks_variables.html#variable-precedence-where-should-i-put-a-variable) 
for further details on variable precedence.

See [BARC-93](https://jira.ceh.ac.uk/browse/BARC-93) for further details.

* The PostgreSQL server version varies depending on which operating system is used and if non-system package sources
are permitted

As the package policy varies between system and non-system package sources, and between operating systems, the version
of PostgreSQL installed is variable between supported operating systems.

For Ubuntu and CentOS machines, a non-system APT package is installed resulting in more recent versions of PostgreSQL
to be installed.

It is a convention of BARC roles to use the latest version of packages. Where a suitable non-system package source is
available it will be used. Otherwise system packages will be used. Suitable non-system packages require a reputable,
maintainer, typically a company or well respected individual. Where non-system packages are used, the variable
*BARC_use_non_system_package_sources* can be set to `false` to always use system packages if this is needed.

*This limitation is **NOT** considered to be significant. Solutions will **NOT** be actively pursued.*
*Pull requests addressing this limitation will be considered.*

See [BARC-98](https://jira.ceh.ac.uk/browse/BARC-112) for further details.

* This role does not support creating production ready PostgreSQL instances

Running a production ready PostgreSQL instance requires more than just configuration and tasks performed by Ansible.
A commitment to supporting such instances and ensuring processes such as backups, replication and access control work
effectively are also required.

For this reason, except in exceptional circumstances, projects and applications **SHOULD NOT** use their own PostgreSQL
instances. Instead managed instances **SHOULD** be preferred, which are available through local ICT or remote providers.

As adding features to this role for backups and replication for example, would not solve the problem of supporting such
processes - these features will be deliberately excluded from this role.

*This limitation is **NOT** considered to be significant. Solutions will **NOT** be actively pursued.*
*Pull requests addressing this limitation will **NOT** be considered.*

See [BARC-113](https://jira.ceh.ac.uk/browse/BARC-113) for further details.

More information on role limitations is available in the
[BARC General Documentation](https://antarctica.hackpad.com/BARC-Overview-and-Policies-SzcHzHvitkt#:h=Role-limitations)

## Usage

### BARC Manifest

By default, BARC roles will record that they have been applied to a system, this is termed the BAS Manifest.
The specific local facts set for this role are:

* `ansible_local.barc_postgresql9_server.general.role_applied` - (boolean) records whether a role has been applied
* `ansible_local.barc_postgresql9_server.general.role_version` - (string) records the version of the role that was applied

Note: You **SHOULD** use this feature to determine whether this role has been applied to a system.
If you do not want these facts to be set by this role, you **MUST** skip the **BARC_SET_MANIFEST** tag.

More information is available in the
[BARC General Documentation](https://antarctica.hackpad.com/BARC-Overview-and-Policies-SzcHzHvitkt#:h=Role-Manifest)

### PostgreSQL version

Depending on the operating system used, the version of PostgreSQL installed will differ, though it will be at least
PostgreSQL *9.2*, and not greater than the last PostgreSQL 9 series release. The table below hopes to clarify the
version you can expect:

| Operating System | Non-System Package Sources Permitted | PostgreSQL version | Notes |
| ---------------- | ------------------------------------ | ------------------ | ----- |
| Ubuntu           | Yes                                  | *9.5*              | -     |
| Ubuntu           | No                                   | *9.3*              | -     |
| CentOS           | Yes                                  | *9.5*              | -     |
| CentOS           | No                                   | *9.2*              | -     |

Because the exact version installed cannot be guaranteed by this role, you should be careful if using depending on this
role in another role or a project that relies on PostgreSQL. If any version of the client greater than 9.2 and less
than 10.0, is acceptable this role is suitable, however where you depend on some feature added to minor releases (e.g.
*9.4*) this role is unsuitable.

This ambiguity is considered a limitation, see the *limitations* section for more information.

### Non-system packages

It is a convention of BARC roles to use the latest version of packages. Where a suitable non-system package source is
available it will be used. Suitable non-system packages require a reputable, maintainer, typically a company or well
respected individual. If this is for some reason unsuitable, it is possible to only use system packages by setting the
*BARC_use_non_system_package_sources* variable to `false`.

Note: As the package policy varies between system and non-system package sources, and between operating systems, the
version of installed packages is variable.

### Firewall configuration

This role assumes the machine it is applied to is using a system firewall. By default this role will generate a firewall
service relevant to PostgreSQL and automatically enable and persist this service to allow access to its services.

Specifically:

* On CentOS machines:
    * A firewalld firewall service will be generated containing rules for PostgreSQL
    * This service will be enabled persistently
    * The firewall service is reloaded
* On Ubuntu machines:
    * A UFW firewall application will be generated containing rules for PostgreSQL
    * This application will be enabled
    * The firewall service is reloaded

Custom generated firewall services are used to ensure if custom listening ports are used (i.e. not port 5432) rules
will reflect the port that is used. The firewall port is controlled by the *postgresql9_server_firewall_port* variable.

If you do not want this role to manage the system firewall, or no system firewall is used, you **MUST** skip the
**BARC_CONFIGURE_FIREWALL** tag when using this role.

Note: If you are using BAS maintained [operating system images](https://github.com/antarctica/packer-vm-templates) this
configuration is fully supported and should be seamless. In other cases additional configuration may be needed depending
on how the system firewall has been configured.

### PostgreSQL configuration

#### `postgresql.conf`

The main PostgreSQL, `postgresql.conf`, is modified in-place by this role, meaning any alterations made by this role 
are performed on a line basis. This means a custom PostgreSQL configuration file is safe to use with this role.

The modifications this role will make to this file are:

* Listen address - the address (IP) on which PostgreSQL will listen for remote client connections on
  * The value of this setting will is controlled by the *postgresql9_server_listen_address* variable.

#### `pg_hba.conf`

This file is used for controlling client access to a PostgreSQL instance. It defines rules for who can access which
databases, and from where and over which protocols.

This role uses an Ansible template to generate this file, to harmonises defaults between CentOS and Ubuntu. 
It is **NOT** safe to change this file outside this role as changes will be overwritten and change events triggered.

An additional rule is added to this file which allows remote connections from any address in the same network on which 
PostgreSQL is listening. In most cases this will effectively allow remote access from a host's peers, but not from 
general remote hosts for example.

Note: There are security implications with this default which are discussed in the *Intended use-cases* section.

### PostgreSQL extensions

This role does not support adding additional PostgreSQL extensions, such as PostGIS or HStore.

Where these extensions are desired, additional BARC roles may be provided if they are used commonly. Otherwise 
extensions can be safely installed outside of this role.

Note: Extensions are enabled on a per-database basis in PostgreSQL and **SHOULD NOT** be enabled within the default
*postgres* database. As this role does not support creating databases, tasks to enable extensions will need to be made
outside of this role.

### PostgreSQL roles

PostgreSQL roles represent, users, groups, or combinations of users and groups.

This role does not support adding additional PostgreSQL roles, as this will depend on the needs of your application and
team. No BARC role for adding PostgreSQL roles will be added, as an Ansible module exists for this purpose, and a role
wrapped around this would not bring any significant benefit.

Note: Setting a password for a role associated with a operating system user account is optional, but required if a user 
will be connecting remotely.

A user with super-user permissions can be added with a task such as:

```yaml
---
- name: create a privileged postgresql role linked to an operating system user
  postgresql_user:
               name="jeff"
           password="password"
    role_attr_flags=SUPERUSER,CREATEROLE,CREATEDB,REPLICATION
              state=present
  become_user: postgres
```

A standard user can be added with a tasks such as:

```yaml
---
- name: create a non-privileged postgresql role for an application
  postgresql_user:
               name="app"
           password="password"
              state=present
  become_user: postgres
```

The default `postgres` role is not modified by this role. Whilst it is safe to modify this user outside this role 
(setting a password, or removing the user entirely), it is not recommended to do so. As this role is not intended for 
production environments (see the *Intended use-cases* section), the potential security risk leaving this default user 
could cause is not considered significant enough.

### PostgreSQL databases

This role does not support adding additional PostgreSQL databases, as this will depend on the needs of your application
and team. No BARC role for adding PostgreSQL databases will be added, as an Ansible module exists for this purpose, and
a role wrapped around this would not bring any significant benefit.

The default database template (i.e. *template0*) is safe to change outside this role if desired.

A typical database can be added with a task such as:

```yaml
---

- name: create database for an application
  postgresql_db:
     name="app"
    owner="app"
    state=present
  become_user: postgres
```

### Intended use-cases

This role is designed to provide a general purpose PostgreSQL installation for non-production environments.

This role **SHOULD NOT** be used in production as a number of features required for production are not managed by this
role, including:

* Backups
* Replication
* Performance tuning & optimisation
* Auditing
* Encryption
* Secure remote access - using TLS for example
* Database management and support

This is considered a limitation, see the *limitations* section for more information.

Instead a suitably managed database instance **SHOULD** be used for production. This will depend on the location and 
provider you are using:

* BAS Cambridge - BAS ICT maintain a managed PostgreSQL instance with commonly used extensions available
  * Contact the [BAS ICT Helpdesk](mailto:helpdesk@bas.ac.uk) for more information and access
* AWS - Managed databases are available through the [AWS Relational Database Service (RDS)](https://aws.amazon.com/rds/)
  * It is strongly recommended to use the BAS AWS account for running this service to gain access to paid AWS support
  * Contact the [BAS Web & Applications Team](mailto:webapps@bas.ac.uk) for more information and access
* Other
  * Contact the [BAS Web & Applications Team](mailto:webapps@bas.ac.uk) for advise

The configuration of the `pg_hba.conf` file used by this role is not sufficiently secure for a production environment,
as depending on the networking environment of the host, a database could be exposed to the general internet.

Due to the above, and for general security, this role is designed to applied to hosts running within private networks.
This may be traditional private networks protected using firewalls, or a private subnet, or provider specific concept.

### Typical playbook

```yaml
---

- name: ...
  hosts: all
  become: yes
  vars: []
  roles:
    - bas-ansible-roles-collection.postgresql9-server
  tasks:
    - name: create a non-privileged postgresql role for an application
      postgresql_user:
        name="app"
        password="password"
        state=present
      become_user: postgres
    - name: create database for an application
      postgresql_db:
        name="app"
        owner="app"
        state=present
      become_user: postgres
```

### Tags

BARC roles use standardised tags to control which aspects of an environment are changed by roles.
Tasks in this role will 'tag' themselves with these tags as appropriate, typically in `tasks/main.yml`.

More information is available in the
[BARC General Documentation](https://antarctica.hackpad.com/BARC-Overview-and-Policies-SzcHzHvitkt#:h=Appendix-B---BARC-Standardised)

### Variables

#### *postgresql9_server_barc_role_name*

* **MUST NOT** be specified
* Specifies the name of this role within the BAS Ansible Roles Collection (BARC) used for setting local facts
* See the *BARC roles manifest* section for more information
* Example: `postgresql9_server`

#### *postgresql9_server_barc_role_version*

* **MUST NOT** be specified
* Specifies the name of this role within the BAS Ansible Roles Collection (BARC) used for setting local facts
* See the *BARC roles manifest* section for more information
* Example: `2.0.0`

### *BARC_use_non_system_package_sources*

* **MAY** be specified
* Specifies whether non-system sources can be used to install packages
* Note: This variable is scoped to all other BARC roles which install packages from non-system sources
* Values MUST use one of these options, as determined by Ansible:
    * `true`
    * `false`
* Values **SHOULD NOT** be quoted to prevent Ansible coercing values to a string
* Where not specified, a value of true will be assumed
* Default: `true`

### *postgresql9_server_non_system_postgres_version*

* **MUST NOT** be specified
* Specifies the major.minor version of PostgreSQL when installed from non-system sources
* Used for constructing the path to the PostgreSQL configuration directory
* The value for this variable **MUST NOT** be changed as the version of PostgreSQL when installed from non-system 
sources is controlled elsewhere in this role, not by this variable, and would only cause failed tasks
* Default: `9.5`

### *postgresql9_server_firewall_port*

* **MAY** be specified
* Specifies the port on which PostgreSQL will listen for requests from clients, used for generating firewall rules
* Values **MUST** be a valid system port, as determined by the operating system
* The default value is a conventional default, other values **SHOULD NOT** be used without good reason
* Default: `5432`

### *postgresql9_server_system_only_postgres_version*

* **MUST NOT** be specified
* Specifies the major.minor version of PostgreSQL when installed from system-only sources
* Used for constructing the path to the PostgreSQL configuration directory
* The value for this variable **MUST NOT** be changed as the version of PostgreSQL when installed from system-only
sources is controlled by the operating system, not by this role, and would only cause failed tasks
* The default value for this variable is set as a role variable, rather than a role default variable, this has 
implications if overriding this value, see the *Limitations* section for more information
* Default:
    * `9.2` - CentOS
    * `9.3` - Ubuntu

### *postgresql9_server_configuration_base*

* **MAY** be specified
* Species the common elements of the path to the PostgreSQL configuration directory, up to the version identifier
* These common elements are typically the path until a PostgreSQL version, may, or may not be specified
* Used for constructing the path to the PostgreSQL configuration directory
* Values **MUST** be a valid system path, as determined by the operating system
* Values **MUST NOT** include a trailing slash (`/`)
* Values **MUST** use absolute paths
* The default value, which varies depending on the machine operating system, is a conventional default, other values 
**SHOULD NOT** be used without good reason
* The default value for this variable is set as a role variable, rather than a role default variable, this has 
implications if overriding this value, see the *Limitations* section for more information
* Default:
    * `/var/lib/pgsql` - CentOS
    * `/etc/postgresql` - Ubuntu

### *postgresql9_server_configuration_directory*

* **MAY** be specified
* Species the common elements of the path to the PostgreSQL configuration directory, following the version identifier
* These common elements are typically a directory after the PostgreSQL version, may, or may not have been specified
* Used for constructing the path to the PostgreSQL configuration directory
* Values **MUST** be a valid system path, as determined by the operating system
* Values **MUST NOT** include a trailing slash (`/`)
* Values **MUST** use absolute paths
* The default value, which varies depending on the machine operating system, is a conventional default, other values 
**SHOULD NOT** be used without good reason
* The default value for this variable is set as a role variable, rather than a role default variable, this has 
implications if overriding this value, see the *Limitations* section for more information
* Default:
    * `data` - CentOS
    * `main` - Ubuntu

### *postgresql9_server_listen_address*

* **MAY** be specified
* Specifies the network interface on which PostgreSQL will listen for remote client connections
* The interface is specified as the IP address of the host on that interface (i.e. 10.0.0.22 etc.)
* Values **MUST** be valid IP addresses assigned to a network interface on the host, as determined by the operating 
system
* It is strongly recommended to use Ansible facts to output the IP addresses of the relevant network interface, or order
to prevent statically defining an address for a dynamic network (i.e. where DHCP is used)
* The default value for this variable is set to the local loop-back interface (i.e. 127.0.0.1) and is effectively not
available remotely
* Default `{{ ansible_lo.ipv4.address }}`

## Developing

### Issue tracking

Issues, bugs, improvements, questions, suggestions and other tasks related to this package are managed through the
[BAS Ansible Roles Collection](https://jira.ceh.ac.uk/projects/BARC) (BARC) project on Jira.

This service is currently only available to BAS or NERC staff, although external collaborators can be added on request.
See our contributing policy for more information.

More information is also available in the
[BARC General Documentation](https://antarctica.hackpad.com/BARC-Overview-and-Policies-SzcHzHvitkt#:h=Issue-Tracking)

### Source code

All changes should be committed, via pull request, to the canonical repository:

`ssh://git@stash.ceh.ac.uk:7999/barc/postgresql9-server.git`

A read-only mirror of this repository is maintained on GitHub:

`git@github.com:bas-ansible-roles-collection/postgresql9-server.git`

More information is available in the
[BARC General Documentation](https://antarctica.hackpad.com/BARC-Overview-and-Policies-SzcHzHvitkt#:h=Source-Code)

### Contributing policy

This project welcomes contributions, see `CONTRIBUTING.md` for our general policy.

### Release procedure

The general release procedure for BARC roles is available in the
[BARC General Documentation](https://antarctica.hackpad.com/BARC-Overview-and-Policies-SzcHzHvitkt#:h=Release-procedures)

## Licence

Copyright 2016 NERC BAS.

Unless stated otherwise, all documentation is licensed under the Open Government License - version 3.
All code is licensed under the MIT license.

Copies of these licenses are included within this project.
