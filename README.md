CGrouper
=========

CGrouper provides tools to manage cgroup cpu and memory settings for multiple services across fleets of servers.

Requirements
------------

Linux distro running systemd - all testing done on Fedora and RHEL
Right now, cgroups version 1 (default through RHEL 8)

Role Variables
--------------

This role takes the variable cgrouper_limits with dictionaries. 

Possible values for each dictionary entry are:
```
sname (no default) - name of the service, such as httpd. You need this one
cpu_shares (default 1024) - controls balance of 
cpu_max (no default) - maximum cpu time. tranlates to "cpu_quota". 100 is 100% of a single core, can be higher (250% for instance is 2 and a half cores. Which is a terrible sitcom)
mem_max (no default) - maximum memory in megabytes
```

Example Playbook
----------------

This playbook will set up the cgroup configs for all web servers so the Apache gets 4 times the processoer time of other daemons when the system is under load. It will also cap the mandatory malware scanner to 10% of a single CPU and will stop RAM Hog 2018 from using more than 512 MB. It will then 

```yml
- name: Apache is important
   hosts: web
   vars:
     cgrouper_limits:
       - { sname: httpd, cpu_shares: 4096 }
       - { sname: mscan, cpu_max: 10 } 
       - { sname: ramhogd, mem_max: 512 }
   roles:
     - CGrouper
```

License
-------

GPL v 3

Author Information
------------------

Original author Marc Richter - mrichter@redhat.com
