Bacula Client
=========

An Ansible role for installing and managing Bacula Client (bacula-fd). This role with work RedHat based systems, and provides file configuration.

Requirements
------------

This role Ansible 2.5+

Role Variables
--------------

### Itsn't using variables ###

Dependencies
------------

### Itsn't using dependencies ###

Example Playbook
----------------

Standard install and default version for the OS:
```yaml
---
- hosts: datacenter01 
  roles:
    - bacula_client
```
```yaml
---
- name: install bacula client
  yum:
    name: bacula-client
    state: present
```

Using the template of configuration bacula-fd.j2, altering value Name to HOSTNAME of the machine
```js2
#
# Default  Bacula File Daemon Configuration file
#
#  For Bacula release 5.2.13 (19 February 2013) -- redhat (Core)
#
# There is not much to change here except perhaps the
# File daemon Name to
#

#
# List Directors who are permitted to contact this File daemon
#
Director {
  Name = baculaetice01-dir
  Password = "baculejo"
}

#
# Restricted Director, used by tray-monitor to get the
#   status of the file daemon
#
Director {
  Name = bacula-mon
  Password = "baculejo"
  Monitor = yes
}

#
# "Global" File daemon configuration specifications
#
FileDaemon {                          # this is me
  Name = {{ ansible_hostname }}
  FDport = 9102                  # where we listen for the director
  WorkingDirectory = /var/spool/bacula
  Pid Directory = /var/run
  Maximum Concurrent Jobs = 20
}

# Send all messages except skipped files back to Director
Messages {
  Name = Standard
  director = bacula-dir = all, !skipped, !restored
}
```
```yaml
---
- name: delivery file configuration bacula-fd
  template:
     src: bacula-fd.j2
     dest: /etc/bacula/bacula-fd.conf
     owner: root
     group: root
     mode: 644
  notify:
        - restart bacula-fd
```
Configuring the service in systemctl and Runlevel.
```yaml
---
- name: configuration service bacula-fd
  service:
    name: bacula-fd
    state: started
    enabled: yes
```
Any alteration on configuration, a restart command is sent to systemctl.
```yaml
---
- name: restart bacula-fd
  service:
     name: bacula-fd
     state: restarted
```
License
-------

[Academic Free License ("AFL") v. 3.0][afl]

[afl]: http://opensource.org/licenses/AFL-3.0

Author Information
------------------

[Diego de Barros] (https://github.com/dabmax)
