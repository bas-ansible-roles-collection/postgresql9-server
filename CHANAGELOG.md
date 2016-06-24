# PostgreSQL 9 Server (`postgresql9-server`) - Change log

All notable changes to this role will be documented in this file.
This role adheres to [Semantic Versioning](http://semver.org/spec/v2.0.0.html).

Remember: Make sure to update postgresql9_server_barc_role_version variable when a new version is released.

## [Unreleased][unreleased]

## 0.2.1 - 24/06/2016

### Added

* Ansible 2 compatibility
* Support for removing failed database initialisations on CentOS

### Fixed

* Minor markdown formatting
* Improved the robustness of dynamic inventories

## 0.2.0 - 06/04/2016

### Added

* Better typical usage in README
* Note on how per-database tasks are required for enabling PostgreSQL extensions

### Fixed

* Minor README corrections
* Logic for conditional tasks
* Syntax highlighting in README

### CHANGED

* Local, non-socket, connections now use MD5 (i.e. password) authentication over the default 'ident' method to support
the use of scripts connecting to a local PostgreSQL instance

## 0.1.0 - 30/03/2016

### Added

* New role! - initial version based on 0.1.0 of the BARC flavour of the BAS Base Project - Pristine
