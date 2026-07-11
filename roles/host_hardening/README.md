linux_security
==============

This role configure basic settings related to Linux security.
I tried to use the default tools for a given distribution (e.g. firewall configuration for Debian based systems is done using UFW and for RedHat I use firewalld).
This approach lead to situation when configuring the same component require using different set of variable according to OS family.

List of components that this role configures:
- Disable root account.
- Add root CA certificate.
- Configure OpenSSH server based on Mozilla recommendation [Modern (OpenSSH 6.7+)](https://infosec.mozilla.org/guidelines/openssh#modern-openssh-67).
- Add sudo users/groups to run all commands without password (optionaly).
- Basic firewall rules using [UFW](https://wiki.ubuntu.com/UncomplicatedFirewall) on Debian/Ubuntu OS or [firewalld](https://firewalld.org/) on RedHat OS (additionaly on RedHat 8 disable AllowZoneDrifting in firewalld configuration).
- Managing autoupdates with [Unattended Upgrades](https://wiki.debian.org/UnattendedUpgrades) on Debian/Ubuntu OS or [DNF Automatic](https://dnf.readthedocs.io/en/latest/automatic.html) on RedHat OS.

Requirements
------------

- ansible-galaxy collection install community.general

Role Variables
--------------

### **Root CA section:**

- `linux_security_root_ca_list` - List of root CA certificates to add or remove from trusted store.

```
linux_security_root_ca_list:
  - name: Certum-CA # CA name
    state: present # absent | present
    content: |
      -----BEGIN CERTIFICATE-----
      MIIDDDCCAfSgAwIBAgIDAQAgMA0GCSqGSIb3DQEBBQUAMD4xCzAJBgNVBAYTAlBM
      MRswGQYDVQQKExJVbml6ZXRvIFNwLiB6IG8uby4xEjAQBgNVBAMTCUNlcnR1bSBD
      QTAeFw0wMjA2MTExMDQ2MzlaFw0yNzA2MTExMDQ2MzlaMD4xCzAJBgNVBAYTAlBM
      MRswGQYDVQQKExJVbml6ZXRvIFNwLiB6IG8uby4xEjAQBgNVBAMTCUNlcnR1bSBD
      QTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAM6xwS7TT3zNJc4YPk/E
      jG+AanPIW1H4m9LcuwBcsaD8dQPugfCI7iNS6eYVM42sLQnFdvkrOYCJ5JdLkKWo
      ePhzQ3ukYbDYWMzhbGZ+nPMJXlVjhNWo7/OxLjBos8Q82KxujZlakE403Daaj4GI
      ULdtlkIJ89eVgw1BS7Bqa/j8D35in2fE7SZfECYPCE/wpFcozo+47UX2bu4lXapu
      Ob7kky/ZR6By6/qmW6/KUz/iDsaWVhFu9+lmqSbYf5VT7QqFiLpPKaVCjF62/IUg
      AKpoC6EahQGcxEZjgoi2IrHu/qpGWX7PNSzVttpd90gzFFS269lvzs2I1qsb2pY7
      HVkCAwEAAaMTMBEwDwYDVR0TAQH/BAUwAwEB/zANBgkqhkiG9w0BAQUFAAOCAQEA
      uI3O7+cUus/usESSbLQ5PqKEbq24IXfS1HeCh+YgQYHu4vgRt2PRFze+GXYkHAQa
      TOs9qmdvLdTN/mUxcMUbpgIKumB7bVjCmkn+YzILa+M6wKyrO7Do0wlRjBCDxjTg
      xSvgGrZgFCdsMneMvLJymM/NzD+5yCRCFNZX/OYmQ6kd5YCQzgNUKD73P9P4Te1q
      CjqTE5s7FCMTY5w/0YcneeVMUeMBrYVdGjux1XMQpNPyvG5k9VpWkKjHDkx0Dy5x
      O/fIR/RpbxXyEV6DHpx8Uq79AtoSqFlnGNu8cN2bsWntgM6JQEhqDjXKKWYVIZQs
      6GAqm4VKQPNriiTsBhYscw==
      -----END CERTIFICATE-----

```

### **OpenSSH section:**

- `linux_security_sshd_allow_users` - list of allowed users to connect using ssh.

```
linux_security_sshd_allow_users:
  - allow_demouser1
  - allow_demouser2
```

- `linux_security_sshd_deny_users`- list of denyed users to connect using ssh.

```
linux_security_sshd_deny_users:
  - deny_demouser1
  - deny_demouser2
```

### **Sudo section:**

- `linux_security_sudo_nopasswd_list` - list of sudo users/groups allowed to run all commands without password.

```
linux_security_sudo_nopasswd_list:
  - name: # user name
    state: # absent | present
```

### **UFW firewall section Debian/Ubuntu OS:**

- `linux_security_ufw_default_list` - list with default settings for incoming/outgoing traffic.

```
linux_security_ufw_default_list:
  - { direction: incoming, policy: deny }
  - { direction: outgoing, policy: allow }
```

- `linux_security_ufw_logging` - toggles logging. Logged packets use the LOG_KERN syslog facility [on, off, low, medium, high, full]

```
linux_security_ufw_logging: 'on'
```

- `linux_security_ufw_rules_list` - list of firewall rules.

```
linux_security_ufw_rules_list:
  - rule: allow # add firewall rule [allow, deny, limit, reject]
    delete: 'no'  # delete rule [yes/no]
    direction: in  # select direction for a rule [in, incoming, out, outgoing, routed]
#    from_ip: 10.20.30.40 # source IP address
#    from_port: 12345  # source port
#    interface: eth0  # specify interface for the rule
#    log: 'yes'  # log new connections matched to this rule [yes/no]
    proto: tcp  # TCP/IP protocol [any, tcp, udp, ipv6, esp, ah, gre, igmp]
#    to_ip: 10.2.3.4  # destination IP address
    to_port: 22  # destination port
    comment: 'Allow SSH'
```

### **FIREWALLD firewall section RedHat OS:**

- `linux_security_firewalld_rules_list` - list of firewall rules.

```
linux_security_firewalld_rules_list:
  - state: enabled # for service and ports [disabled, enabled] for zones [present, absent]
    # icmp_block: # icmp types to block "firewall-cmd --get-icmptypes"
    # icmp_block_inversion: # [Enable/Disable] inversion of ICMP blocks for a zone in firewalld
    # immediate: # [yes/no] should this configuration be applied immediately, if set as permanent (default no)
    # interface: # interface to add/remove to/from a zone
    # offline: # [yes/no] whether to run this module even when firewalld is offline.
    permanent: yes # [yes/no] should this configuration be in the running firewalld configuration or persist across reboots
    # port: # port or port range to add/remove to/from firewalld [PORT/PROTOCOL, PORT-PORT/PROTOCOL]
    # rich_rule: # rich rule to add/remove to/from firewalld https://firewalld.org/documentation/man-pages/firewalld.richlanguage.html
    service: ssh # service name to add/remove "firewall-cmd --get-services"
    # source: # source ip/network
    # target: # firewalld Zone target [default, ACCEPT, DROP, %%REJECT%%]
    # timeout: # the amount of time in seconds the rule should be in effect for when non-permanent
    zone: public # firewalld zone to add/remove to/from [block, dmz, drop, external, home, internal, public, trusted, work] (default public)
```

### **Unattended Upgrades section Debian/Ubuntu OS:**

- `linux_security_apt_origins_pattern_debian_list` - controls which packages are upgraded on Debian OS.

```
linux_security_apt_origins_pattern_debian_list:
  - '"origin=Debian,codename=${distro_codename},label=Debian";'
  - '"origin=Debian,codename=${distro_codename},label=Debian-Security";'
```

- `linux_security_apt_allowed_origins_ubuntu_list` - controls which packages are upgraded on Ubuntu OS.

```
linux_security_apt_allowed_origins_ubuntu_list:
  - '"${distro_id}:${distro_codename}";'
  - '"${distro_id}:${distro_codename}-security";'
```

- `linux_security_apt_allowed_origins_esm_ubuntu_list` - controls which packages are upgraded on Ubuntu OS with Extended Security Maintenance.

```
linux_security_apt_allowed_origins_esm_ubuntu_list:
  - '"${distro_id}ESMApps:${distro_codename}-apps-security";'
  - '"${distro_id}ESM:${distro_codename}-infra-security";'
```

- `linux_security_apt_package_black_list` - list of regular expressions, matching packages to exclude from upgrading.

```
linux_security_apt_package_black_list:
  - libc6$
  - libc6-dev$
  - libstdc\+\+6$
```

- `linux_security_apt_notification_email` - notification email address (default no notification) require maile setup.

```
linux_security_apt_notification_email: alerts@example.com
```

- `linux_security_apt_mail_only_on_error_debian` - [true/false] [string] send emails only on errors (default false).

```
linux_security_apt_mail_only_on_error_debian: "true"
```

- `linux_security_apt_mail_only_on_error_ubuntu` - ["always", "only-on-error", "on-change"] when send emails (default on-change).

```
linux_security_apt_mail_only_on_error_ubuntu: always
```

- `linux_security_apt_remove_unused_kernel_packages` - [true/false] [string] remove unused automatically installed kernel-related packages (default true).

```
linux_security_apt_remove_unused_kernel_packages: "true"
```

- `linux_security_apt_remove_new_unused_dependencies` - [true/false] [string] automatic removal of newly unused dependencies after the upgrade (default true).

```
linux_security_apt_remove_new_unused_dependencies: "true"
```

- `linux_security_apt_remove_unused_dependencies` - [true/false] [string] automatic removal of unused packages after the upgrade (default false).

```
linux_security_apt_remove_unused_dependencies: "true"
```

- `linux_security_apt_automatic_reboot` - [true/false] [string] automatically reboot (default true).

```
linux_security_apt_automatic_reboot: "true"
```

- `linux_security_apt_automatic_reboot_with_users` - [true/false] [string] automatically reboot even if there are users currently logged in (default true).

```
linux_security_apt_automatic_reboot_with_users: "true"
```

- `linux_security_apt_automatic_reboot_time`- reboot time after installing updates.

```
linux_security_apt_automatic_reboot_time: "04:00"
```

- `linux_security_apt_dl_limit` - apt bandwidth limit kb/sec (default 70kb/sec).

```
linux_security_apt_dl_limit: 140
```

- `linux_security_apt_syslog_enable` - [true/false] [string] enable logging to syslog (default false).

```
linux_security_apt_syslog_enable: "false"
```

- `linux_security_apt_syslog_facility` - syslog facility (default daemon).

```
linux_security_apt_syslog_facility: daemon
```

- `linux_security_apt_allow_downgrade_ubuntu` - [true/false] [string] allow package downgrade if Pin-Priority exceeds 1000 (default false).

```
linux_security_apt_allow_downgrade_ubuntu: "false"
```

- `linux_security_apt_download_time` - systemd timer override default download time.

```
linux_security_apt_download_time: "5,20:00"
```

- `linux_security_apt_download_randomized_delay_sec` - systemd timer override default randomized deley.

```
linux_security_apt_download_randomized_delay_sec: "2h"
```

- `linux_security_apt_upgrade_time` - systemd timer override default upgrade time.

```
linux_security_apt_upgrade_time: "2:00"
```

- `linux_security_apt_upgrade_randomized_delay_sec` - systemd timer override default upgrade deley.

```
linux_security_apt_upgrade_randomized_delay_sec: "60m"
```

### **DNF-automatic section RedHat OS:**

- `linux_security_dnf_automatic_upgrade_type` - [default, security] what kind of upgrade to perform.

```
linux_security_dnf_automatic_upgrade_type: default
```

- `linux_security_dnf_automatic_random_sleep` - random sleep in seconds.

```
linux_security_dnf_automatic_random_sleep: 120
```

- `linux_security_dnf_automatic_network_online_timeout` - maximum time in seconds to wait until the system is on-line and able to connect to remote repositories.

```
linux_security_dnf_automatic_network_online_timeout: 120
```

- `linux_security_dnf_automatic_download_updates` - [yes/no] [string] whether updates should be downloaded when they are available.

```
linux_security_dnf_automatic_download_updates: 'yes'
```

- `linux_security_dnf_automatic_apply_updates` - [yes/no] [string] whether updates should be applied when they are available.

```
linux_security_dnf_automatic_apply_updates: 'yes'
```

- `linux_security_dnf_automatic_emit_via` - [stdio, email, motd] how to send messages.

```
linux_security_dnf_automatic_emit_via: email
```

- `linux_security_dnf_automatic_system_name` - name to use for this system in messages that are emitted (default hostname).

```
linux_security_dnf_automatic_system_name: demohost
```

- `linux_security_dnf_automatic_email_from` - the address to send email messages from (default using {{ ansible_hostname }}@{{ linux_common_mta_sender_domain }} from role linux_common).

```
linux_security_dnf_automatic_email_from: demohost@example.com
```

- `linux_security_dnf_automatic_email_to_list` - list of addresses to send messages to (default using linux_common_mta_root_notification_email from role linux_common if defined).

```
linux_security_dnf_automatic_email_to_list:
  - alerts@example.com
  - admins@example.com
```

- `linux_security_dnf_automatic_email_host` - name of the host to connect to send email messages (default using linux_common_mta_smtp_relayhost from role linux_common).

```
linux_security_dnf_automatic_email_host: smtp.example.com
```

- `linux_security_dnf_automatic_install_time` - systemd timer override default autoupdate time.

```
linux_security_dnf_automatic_install_time: "2:00"
```

- `linux_security_dnf_automatic_install_randomized_delay_sec` - systemd timer override default autoupdate deley.

```
linux_security_dnf_automatic_install_randomized_delay_sec: "60m"
```

Dependencies
------------

Role linux_common should be run before if you want to use email notification for autoupdate.

Example Playbook
----------------

This example playbook uses my other role `linux_common` (which is also in this repository) and `linux_security`.

```
- name: Example
  hosts: servers
  become: true
  gather_facts: true

  vars:
    # Role linux_common
    linux_common_domain_name: example.local
    linux_common_mta_enabled: yes
    linux_common_mta_root_notification_email: alerts@example.local
    linux_common_mta_smtp_relayhost: smtp.example.local
    linux_common_mta_sender_domain: example.local
    # Role linux_security:
    linux_security_root_ca_list:
      - name: Certum-CA
        state: present
        content: |
          -----BEGIN CERTIFICATE-----
          MIIDDDCCAfSgAwIBAgIDAQAgMA0GCSqGSIb3DQEBBQUAMD4xCzAJBgNVBAYTAlBM
          MRswGQYDVQQKExJVbml6ZXRvIFNwLiB6IG8uby4xEjAQBgNVBAMTCUNlcnR1bSBD
          QTAeFw0wMjA2MTExMDQ2MzlaFw0yNzA2MTExMDQ2MzlaMD4xCzAJBgNVBAYTAlBM
          MRswGQYDVQQKExJVbml6ZXRvIFNwLiB6IG8uby4xEjAQBgNVBAMTCUNlcnR1bSBD
          QTCCASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAM6xwS7TT3zNJc4YPk/E
          jG+AanPIW1H4m9LcuwBcsaD8dQPugfCI7iNS6eYVM42sLQnFdvkrOYCJ5JdLkKWo
          ePhzQ3ukYbDYWMzhbGZ+nPMJXlVjhNWo7/OxLjBos8Q82KxujZlakE403Daaj4GI
          ULdtlkIJ89eVgw1BS7Bqa/j8D35in2fE7SZfECYPCE/wpFcozo+47UX2bu4lXapu
          Ob7kky/ZR6By6/qmW6/KUz/iDsaWVhFu9+lmqSbYf5VT7QqFiLpPKaVCjF62/IUg
          AKpoC6EahQGcxEZjgoi2IrHu/qpGWX7PNSzVttpd90gzFFS269lvzs2I1qsb2pY7
          HVkCAwEAAaMTMBEwDwYDVR0TAQH/BAUwAwEB/zANBgkqhkiG9w0BAQUFAAOCAQEA
          uI3O7+cUus/usESSbLQ5PqKEbq24IXfS1HeCh+YgQYHu4vgRt2PRFze+GXYkHAQa
          TOs9qmdvLdTN/mUxcMUbpgIKumB7bVjCmkn+YzILa+M6wKyrO7Do0wlRjBCDxjTg
          xSvgGrZgFCdsMneMvLJymM/NzD+5yCRCFNZX/OYmQ6kd5YCQzgNUKD73P9P4Te1q
          CjqTE5s7FCMTY5w/0YcneeVMUeMBrYVdGjux1XMQpNPyvG5k9VpWkKjHDkx0Dy5x
          O/fIR/RpbxXyEV6DHpx8Uq79AtoSqFlnGNu8cN2bsWntgM6JQEhqDjXKKWYVIZQs
          6GAqm4VKQPNriiTsBhYscw==
          -----END CERTIFICATE-----
    linux_security_enable_autoupdates: yes
    linux_security_apt_notification_email: demotest@example.local
    linux_security_dnf_automatic_email_to_list:
      - alerts@example.com
      - admins@example.com
    linux_security_ufw_rules_list:
      - rule: allow
        delete: 'no'
        direction: in
        proto: tcp
        to_port: 22
        comment: 'Demo SSH'
      - rule: allow
        delete: 'no'
        direction: in
        from_ip: 10.10.100.10
        proto: tcp
        to_port: 443
        comment: 'Demo HTTPS'
    linux_security_firewalld_rules_list:
      - state: enabled
        immediate: yes
        permanent: yes
        service: ssh
        zone: public
      - state: enabled
        immediate: yes
        permanent: yes
        rich_rule: rule family="ipv4" source address="10.10.100.23" port port="443" protocol="tcp" accept
        zone: public

  roles:
    - linux_common
    - linux_security
```

License
-------

MIT / BSD

Author Information
------------------

Kazimierz Biskup [GitHub](https://github.com/kazikb/ansible)
