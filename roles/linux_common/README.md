linux_common
============

This role configure some basic system settings.
I tried to use the default tools for a given distribution (e.g. ntp configuration for Debian based systems is done using systemd-timesyncd and for RedHat I use chronyd).
This approach lead to situation when configuring the same component require using different set of variable according to OS family.

List of components that this role configures:
- Install some additional tools.
- On Debian/Ubuntu OS generate additional locally and setup vim as default editor.
- On RedHat OS add [EPEL](https://docs.fedoraproject.org/en-US/epel/) repository and install additional language packs.
- Setup NTP client (systemd-timesyncd on Debian/Ubuntu OS or chronyd on RedHat OS).
- Setup system timezone.
- Setup default locale.
- Setup host domain name.
- Setup vim editor global config.
- Add/remove additional groups (optionally).
- Setup postfix as send only mta [null client](https://www.postfix.org/STANDARD_CONFIGURATION_README.html#null_client).

Requirements
------------

- ansible-galaxy collection install community.general

Role Variables
--------------

- `linux_common_software_list` - common software to install.

```
linux_common_software_list:
  - vim
  - htop
  - curl
  - tree
```

- `linux_common_domain_name` - set host domain name.

```
linux_common_domain_name: example.com
```

- `linux_common_timezone` - set system timezone.

```
linux_common_timezone: Europe/Warsaw
```

- `linux_common_debian_locale_to_generate` - on Debian/Ubuntu OS list of locales to generate.

```
linux_common_debian_locale_to_generate:
  - name: en_US # locale name base on /usr/share/i18n/SUPPORTED
    state: present # absent | present
  - name: en_US.UTF-8 # locale name base on /usr/share/i18n/SUPPORTED
    state: present # absent | present
  - name: pl_PL # locale name base on /usr/share/i18n/SUPPORTED
    state: present # absent | present
  - name: pl_PL.UTF-8 # locale name base on /usr/share/i18n/SUPPORTED
    state: present # absent | present
```

- `linux_common_redhat_locale_to_install` - on RedHat OS list of additionals language pack to install.

```
linux_common_redhat_locale_to_install:
  - name: glibc-langpack-pl.x86_64 # dnf package name (dnf list | grep glibc-langpack-)
    state: present # absent | present
```

- `linux_common_locale` - set default locale.

```
linux_common_locale: en_US.UTF-8
```

- `linux_common_group_list` - list of groups to add/remove.

```
linux_common_group_list:
  - name: dwmogroup1 # group name
    state: present # absent | present
  - name: dwmogroup1 # group name
    state: absent # absent | present
```

### **systemd-timesyncd section Debian/Ubuntu OS:**

- `linux_common_debian_ntp_server_list` - list of ntp server to add in timesyncd.conf section: NTP=.

```
linux_common_debian_ntp_server_list:
  - ntp1.example.com
  - ntp2.example.com
```

- `linux_common_debian_ntp_server_fallback_ntp_list` - timesyncd.conf section: FallbackNTP=.

```
linux_common_debian_ntp_server_fallback_ntp_list:
  - 0.debian.pool.ntp.org
  - 1.debian.pool.ntp.org
  - 2.debian.pool.ntp.org
  - 3.debian.pool.ntp.org
```

- `linux_common_debian_ntp_root_distance_max_sec` - timesyncd.conf section: RootDistanceMaxSec=.

```
linux_common_debian_ntp_root_distance_max_sec: 5
```

- `linux_common_debian_ntp_poll_interval_min_sec` - timesyncd.conf section: PollIntervalMinSec=.

```
linux_common_debian_ntp_poll_interval_min_sec: 32
```

- `linux_common_debian_ntp_poll_interval_max_sec` - timesyncd.conf section: PollIntervalMaxSec=.

```
linux_common_debian_ntp_poll_interval_max_sec: 2048
```

### **chronyd section RedHat OS:**

- `linux_common_redhat_ntp_server_list` - list of ntp servers|pools to add in chrony.conf allong with options "server ntp.mydomain.local iburst prefer"|"pool 2.centos.pool.ntp.org iburst".

```
linux_common_redhat_ntp_server_list:
  - server ntp.mydomain.local iburst prefer
  - server ntp2.mydomain.local iburst
```

- `linux_common_redhat_ntp_makestep` - allow the system clock to be stepped in the first three updates if its offset is larger than 1 second.

```
linux_common_redhat_ntp_makestep: "1.0 3"
```

- `linux_common_redhat_ntp_hwtimestamp_list` - enable hardware timestamping on all interfaces that support it.

```
linux_common_redhat_ntp_hwtimestamp_list:
  - eth0
  - eth1 txcomp 300e-9 rxcomp 645e-9
  - *
```

- `linux_common_redhat_ntp_minsources` - increase the minimum number of selectable sources required to adjust the system clock.

```
linux_common_redhat_ntp_minsources: 2
```

- `linux_common_redhat_ntp_allow_list` - allow NTP client access from local network.

```
linux_common_redhat_ntp_allow_list:
  - 192.168.10.0/24
  - 10.10.10.1
```

- `linux_common_redhat_ntp_local` - serve time even if not synchronized to a time source.

```
linux_common_redhat_ntp_local: stratum 10
```

- `linux_common_redhat_ntp_log_dir` - specify directory for log files.

```
linux_common_redhat_ntp_log_dir: /var/log/chrony
```

- `linux_common_redhat_ntp_log` - select which information is logged.

```
linux_common_redhat_ntp_log: measurements statistics tracking
```

### **postfix section:**

- `linux_common_mta_enabled` - [yes/no] enable postfix mta for sending notification.

```
linux_common_mta_enabled: yes
```

- `linux_common_mta_root_notification_email` - notification email address for root.

```
linux_common_mta_root_notification_email: alerts@example.com
```

- `linux_common_mta_smtp_relayhost` - smtp relayhost address.

```
linux_common_mta_smtp_relayhost: smtp.example.com
```

- `linux_common_mta_sender_domain` - sender domain name.

```
linux_common_mta_sender_domain: example.com
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
    # Role linux_common
    linux_common_debian_ntp_server_list:
      - ntp1.example.com
      - ntp2.example.com
    linux_common_redhat_ntp_server_list:
      - server ntp1.example.com iburst prefer
      - server ntp2.example.com iburst
    linux_common_domain_name: example.local
    linux_common_group_list:
      - name: dwmogroup1
        state: present
      - name: dwmogroup2
        state: absent
    linux_common_mta_enabled: yes
    linux_common_mta_root_notification_email: alerts@example.local
    linux_common_mta_smtp_relayhost: smtp.example.local
    linux_common_mta_sender_domain: example.local

  roles:
    - linux_common
```

License
-------

MIT / BSD

Author Information
------------------

Kazimierz Biskup [GitHub](https://github.com/kazikb/ansible)
