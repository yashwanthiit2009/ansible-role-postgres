# postgres

Install and configure postgres on your system.

|Travis|GitHub|Quality|Downloads|
|------|------|-------|---------|
|[![travis](https://travis-ci.com/robertdebock/ansible-role-postgres.svg?branch=master)](https://travis-ci.com/robertdebock/ansible-role-postgres)|[![github](https://github.com/robertdebock/ansible-role-postgres/workflows/Ansible%20Molecule/badge.svg)](https://github.com/robertdebock/ansible-role-postgres/actions)|[![quality](https://img.shields.io/ansible/quality/42173)](https://galaxy.ansible.com/robertdebock/postgres)|[![downloads](https://img.shields.io/ansible/role/d/42173)](https://galaxy.ansible.com/robertdebock/postgres)|

## Example Playbook

This example is taken from `molecule/resources/converge.yml` and is tested on each push, pull request and release.
```yaml
---
- name: Converge
  hosts: all
  become: yes
  gather_facts: yes

  roles:
    - role: robertdebock.postgres
```

The machine may need to be prepared using `molecule/resources/prepare.yml`:
```yaml
---
- name: Converge
  hosts: all
  become: yes
  gather_facts: no

  roles:
    - role: robertdebock.bootstrap
    - role: robertdebock.buildtools
    - role: robertdebock.epel
    - role: robertdebock.python_pip
```

For verification `molecule/resources/verify.yml` run after the role has been applied.
```yaml
---
- name: Verify
  hosts: all
  become: yes
  gather_facts: yes

  roles:
    - role: robertdebock.postgres
      postgres_databases:
        - name: test_db
      postgres_users:
        - name: test_user_with_pass
          password: "MyCoMpLeXpAsSwOrD"
        - name: test_user_with_db
          password: "MyCoMpLeXpAsSwOrD"
          db: test_db
      postgres_hba_entries:
        - type: local
          database: all
          user: all
          method: peer
        - type: host
          database: all
          user: all
          address: 127.0.0.1/32
          method: ident
        - type: host
          database: all
          user: all
          address: ::1/128
          method: ident
        - type: local
          database: replication
          user: all
          method: peer
        - type: host
          database: replication
          user: all
          address: 127.0.0.1/32
          method: ident
        - type: host
          database: replication
          user: all
          address: ::1/128
          method: ident
```

Also see a [full explanation and example](https://robertdebock.nl/how-to-use-these-roles.html) on how to use these roles.

## Role Variables

These variables are set in `defaults/main.yml`:
```yaml
---
# defaults file for postgres

postgres_settings:
  - name: port
    value: 5432
  - name: listen_addresses
    value: 127.0.0.1
  - name: unix_socket_directories
    value: /var/run/postgresql
  - name: max_wal_size
    value: 1GB
  - name: min_wal_size
    value: 80MB
  - name: log_timezone
    value: UCT
  - name: datestyle
    value: iso, ymd
  - name: timezone
    value: UCT
  - name: default_text_search_config
    value: pg_catalog.english

postgres_hba_entries:
  - type: local
    database: all
    user: all
    method: peer
  - type: host
    database: all
    user: all
    address: 127.0.0.1/32
    method: ident
  - type: host
    database: all
    user: all
    address: ::1/128
    method: ident
  - type: local
    database: replication
    user: all
    method: peer
  - type: host
    database: replication
    user: all
    address: 127.0.0.1/32
    method: ident
  - type: host
    database: replication
    user: all
    address: ::1/128
    method: ident
```

## Requirements

- Access to a repository containing packages, likely on the internet.
- A recent version of Ansible. (Tests run on the current, previous and next release of Ansible.)

The following roles can be installed to ensure all requirements are met, using `ansible-galaxy install -r requirements.yml`:

```yaml
---
- robertdebock.bootstrap
- robertdebock.buildtools
- robertdebock.epel
- robertdebock.python_pip

```

## Context

This role is a part of many compatible roles. Have a look at [the documentation of these roles](https://robertdebock.nl/) for further information.

Here is an overview of related roles:
![dependencies](https://raw.githubusercontent.com/robertdebock/drawings/artifacts/postgres.png "Dependency")

## Compatibility

This role has been tested on these [container images](https://hub.docker.com/):

|container|tags|
|---------|----|
|alpine|all|
|debian|all|
|fedora|all|
|opensuse|all|
|ubuntu|bionic|

The minimum version of Ansible required is 2.8 but tests have been done to:

- The previous version, on version lower.
- The current version.
- The development version.

## Exceptions

Some variarations of the build matrix do not work. These are the variations and reasons why the build won't work:

| variation                 | reason                 |
|---------------------------|------------------------|
| EL | No package postgresql-server available |
| amazonlinux:1 | /etc/init.d/postgresql: line 37: /etc/sysconfig/network: No such file or directory |
| amazonlinux | Dependency (python_pip) no supported. |


## Testing

[Unit tests](https://travis-ci.com/robertdebock/ansible-role-postgres) are done on every commit, pull request, release and periodically.

If you find issues, please register them in [GitHub](https://github.com/robertdebock/ansible-role-postgres/issues)

Testing is done using [Tox](https://tox.readthedocs.io/en/latest/) and [Molecule](https://github.com/ansible/molecule):

[Tox](https://tox.readthedocs.io/en/latest/) tests multiple ansible versions.
[Molecule](https://github.com/ansible/molecule) tests multiple distributions.

To test using the defaults (any installed ansible version, namespace: `robertdebock`, image: `fedora`, tag: `latest`):

```
molecule test

# Or select a specific image:
image=ubuntu molecule test
# Or select a specific image and a specific tag:
image="debian" tag="stable" tox
```

Or you can test multiple versions of Ansible, and select images:
Tox allows multiple versions of Ansible to be tested. To run the default (namespace: `robertdebock`, image: `fedora`, tag: `latest`) tests:

```
tox

# To run CentOS (namespace: `robertdebock`, tag: `latest`)
image="centos" tox
# Or customize more:
image="debian" tag="stable" tox
```

## License

Apache-2.0


## Author Information

[Robert de Bock](https://robertdebock.nl/)
