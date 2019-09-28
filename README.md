CGrouper
=========

CGrouper provides tools to manage cgroup cpu and memory settings for multiple services across fleets of servers.

It's funny because right now, the systemd module does not allow one to execute the "set-property" sub command, which would let you just do this type of stuff with that module. Anyway, I think I've put a pretty cool spin on it, so enjoy!

Total Ansible n00b, so feel free to modify or send me a PR to update.

Requirements
------------

Linux distro running systemd - all testing done on Fedora and RHEL
Right now, cgroups version 1 (default through RHEL 8)

Role Variables
--------------

This role takes the variable cgrouper_limits as a list.

Possible values for each list entry are:
```
service - name of the service, such as httpd. You need this one

setting - the actual cgroup setting we're changing. Currently three are supported
   - CPUShares - This controls the balance of processer time against other services when the system is under load. Default value on RHEL/Fedora is 1024 
   - CPUQuota - maximum cpu time. tranlates to "cpu_quota". 100% is 100% of a single core, can be higher (250% for instance is 2 and a half cores. Which is a terrible sitcom). Setting ends with a percent sign, do not forget to use it
   - MemoryLimit - maximum memory the service can cosume. End with M for megabytes and G for gigabytes

value - the setting for the cgroup. Note that CPUQuota MUST be expressed with a % after the number. You'll get an error running your playbook if you don't.
```

Example Playbook
----------------

This playbook will set up the cgroup configs for all web servers so the Apache gets 4 times the processoer time of other daemons when the system is under load. It will also cap the mandatory malware scanner to 10% of a single CPU and will stop RAM Hog 2018 from using more than 512 MB. It will then 

```yml
- name: Apache is important
   hosts: web
   vars:
     cgrouper_limits:
       - service: httpd
         setting: CPUShares
         value: 2048
       - service: mscan
         setting: CPUQuota
         value: 10% 
       - service: ramhogd
         setting: MemoryLimit
         value: 512M
   roles:
     - CGrouper
```

License
-------

GPL v 3

Author Information
------------------

Original author Marc Richter - mrichter@redhat.com
