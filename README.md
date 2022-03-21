Role Name
=========

This role installs Docker container with [Gitea](https://gitea.io/).

Requirements
------------

Docker container engine and Python's Docker module installed on target host

Role Variables
--------------

see [./defaults/main.yml](./defaults/main.yml)

Dependencies
------------

No specific dependencies

Example Playbook
----------------

```
- hosts:
    - gitea.example.com
  become: yes
  roles:
    - role: almaops.ct_gitea
```

License
-------

[MIT License](./LICENSE)
