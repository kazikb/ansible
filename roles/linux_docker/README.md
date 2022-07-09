linux_docker
============

This role installs `docker-ce` and `docker-compose` along with their dependencies.
On RedHat based OS removes `podman` and `runc` before performing installation of docker.

Requirements
------------

None.

Role Variables
--------------

- `linux_docker_default_bip` - gateway ip address used by docker default network.

```
linux_docker_default_bip: "172.18.0.1/16"
```

# Docker compose https://github.com/docker/compose/releases

- `linux_docker_compose_version` - docker compose version to download (https://github.com/docker/compose/releases).

```
linux_docker_compose_version: 2.3.3
```

- `linux_docker_compose_checksum` - docker compose checksum.

```
linux_docker_compose_checksum: sha256:d31e90dda58e21a6463cb918868421b4b58c32504b01b1612d154fe6a9167a91
```

Dependencies
------------

None.

Example Playbook
----------------

```
- name: Example
  hosts: servers
  become: true
  gather_facts: true

  vars:
    # Role linux_docker
    linux_docker_default_bip: "172.18.0.1/16"
    linux_docker_compose_version: 2.6.1
    linux_docker_compose_checksum: sha256:ed79398562f3a80a5d8c068fde14b0b12101e80b494aabb2b3533eaa10599e0f

  roles:
    - linux_docker
```

License
-------

MIT / BSD

Author Information
------------------

Kazimierz Biskup [GitHub](https://github.com/kazikb/ansible)
