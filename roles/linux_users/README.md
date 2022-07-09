linux_users
===========

This role manage users accounts and their ssh authorized keys.

Ansible documentation mention sevral options to generate passwords [link](https://docs.ansible.com/ansible/latest/reference_appendices/faq.html#how-do-i-generate-encrypted-passwords-for-the-user-module). To use `mkpasswd` on Ubuntu package `whois` must be installed.

```
sudo apt install whois

mkpasswd --method=sha-512
```

Requirements
------------

None.

Role Variables
--------------

- `linux_users_list` - list of users to add/remove.
```
linux_users_list:
  - username: ansiuser1 # user account name
    password: $6$5T7Eovy5f$Mmqhsr8jmipVpwkvq6qv.EyvWHEkFCkmYv.4w.lWNjws4UW/osDLgt2QmtksXVT1.C26JBH4KmdE1M9lkU7xZ0 # password generated with mkpasswd
    comment: Ansible User 1 # description (aka GECOS) of user account
    state: present # absent | present
    # remove: # yes | no remove user home directory when state absent
    keys: # list of ssh public key
      - pubkey: ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIDtHSFihy9vXkVb4lemAfNBv4NGM/EdAjtIVr9EQBm5t Ansible Demo User # ssh public key
        state: present # absent | present
    shell: /bin/bash # shell
    groups: wheel # default groups (Debian 10: cdrom,floppy,audio,dip,video,plugdev,netdev,bluetooth) (Ubuntu 20.04: adm,cdrom,sudo,dip,plugdev,lxd)
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
    # Role linux_users
    linux_users_list:
      - username: ansiuser4
        password: $6$5T7Eovy5f$Mmqhsr8jmipVpwkvq6qv.EyvWHEkFCkmYv.4w.lWNjws4UW/osDLgt2QmtksXVT1.C26JBH4KmdE1M9lkU7xZ0
        comment: Ansible User 4
        state: present
        remove: yes
        keys:
          - pubkey: ssh-ed25519 AAAAC3NzaC1lZDI1NTE5AAAAIDtHSFihy9vXkVb4lemAfNBv4NGM/EdAjtIVr9EQBm5t Ansible Demo User
            state: present
        shell: /bin/bash
        groups: wheel, cdrom

  roles:
    - linux_users
```

License
-------

MIT / BSD

Author Information
------------------

Kazimierz Biskup [GitHub](https://github.com/kazikb/ansible)
