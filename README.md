# almaops.ct_gitea

This role creates Docker container with [Gitea](https://gitea.io/).

# Requirements

Docker container engine and Python's Docker module installed on target host

# Role Variables
Full list of variables available in [./defaults/main.yml](./defaults/main.yml)

## ct_gitea_template_env
```
ct_gitea_template_env: gitea.env.j2
```
Container's .env file template.

## ct_gitea_path_*
```
ct_gitea_path_config_base: "/etc/ct_gitea"
```
Directory, where containers' .env files are stored.  

```
ct_gitea_path_config_file: "{{ ct_gitea_path_config_base }}/{{ ct_gitea_file_env }}"
```
Absolute path to container's .env file.  

```
ct_gitea_path_base: "/srv/ct_gitea"
```
Base directory, where data volumes are kept.

```
ct_gitea_path_data: "{{ ct_gitea_path_base }}/{{ ct_gitea_ct_name }}"
```
Absolute path of data directory mounted to container.

```
ct_gitea_path_ct_data: "/data"
```
Gitea's data directory inside container. If you change Gitea's data directory path via environment variables, the value of this variable should be adjusted accordingly.

## ct_gitea_bind_*
```
ct_gitea_bind_addr: "127.0.0.1"
```
Default address container's ports are binded to.

```
ct_gitea_bind_addr_www: "{{ ct_gitea_bind_addr }}"
ct_gitea_bind_addr_ssh: "{{ ct_gitea_bind_addr }}"
```
These variables allow to bind web interface and SSH service to separate IP addresses.

```
ct_gitea_bind_port_www: "3000"
ct_gitea_bind_port_ssh: "2220"
```
Web and SSH ports container listens on.

```
ct_gitea_bind_ct_port_www: "3000"
ct_gitea_bind_ct_port_ssh: "22"
```
Web and SSH ports inside container. If you change Gitea listen ports via environment variables, these values should be adjusted accordingly.

## ct_gitea_ct_*
```
ct_gitea_ct_name: "gitea"
ct_gitea_ct_image: "gitea/gitea"
ct_gitea_ct_version: "1.16.4"
ct_gitea_ct_restart_policy: "always"
ct_gitea_ct_state: "started"
ct_gitea_ct_restart: "no"
ct_gitea_ct_pull: "no"
ct_gitea_ct_recreate: "no"
```
Container settings passed to Ansible's `docker_container` module.

## ct_gitea_ct_volume_*
```
ct_gitea_ct_volume_data:
  - "{{ ct_gitea_path_data }}:{{ ct_gitea_path_ct_data }}"
```
Data directory mount to container

```
ct_gitea_ct_volume_tz:
  - "/etc/timezone:/etc/timezone:ro"
  - "/etc/localtime:/etc/localtime:ro"
```
Timezone settings mount to container.

```
# Default value
ct_gitea_ct_volume_extra: []

# Mounting separate volume for storing uploaded avatars
ct_gitea_ct_volume_extra:
  - "/data/gitea-avatars:{{ ct_gitea_path_ct_data }}/avatars"
```
Variable allows to mount arbitrary volumes to container (for example for storing avatars separately). Same format as for `volumes` parameter of `docker_container` module.


## ct_gitea_ct_ports*
```
# Default value
ct_gitea_ct_ports_extra: []
```
Variable allows to proxy arbitrary ports to Gitea container. Same format as for `ports` parameter of `docker_container` module.

```
# Default value
ct_gitea_ct_ports_www:
  - "{{ ct_gitea_bind_addr_www }}:{{ ct_gitea_bind_port_www }}:{{ ct_gitea_bind_ct_port_www }}"
ct_gitea_ct_ports_ssh:
  - "{{ ct_gitea_bind_addr_ssh }}:{{ ct_gitea_bind_port_ssh }}:{{ ct_gitea_bind_ct_port_ssh }}"

# To disable SSH port
ct_gitea_ct_ports_ssh: []
```
Default port mapping for WWW and SSH ports.

## Environment variables
```
# APP_NAME
ct_gitea_env_app_name: "Gitea: Git with a cup of tea"
```
Gitea app display name.

```
# USER_UID
ct_gitea_env_user_uid: "1000"
# USER_GID
ct_gitea_env_user_gid: "1000"
```
User's UID and GID Gitea process runs as. Data directory by default is created to be owned by this UID/GID.

## ct_gitea_env_extra
`{{ ct_gitea_env_extra }}` is a special variable, which allows to define arbitrary set of environment variables for Gitea container.

```
# Example how to configure mailer settings
ct_gitea_env_extra:
  GITEA__mailer__ENABLED: true
  GITEA__mailer__FROM: "Gitea <noreply@example.com>"
  GITEA__mailer__MAILER_TYPE: smtp
  GITEA__mailer__HOST: "mail.example:587"
  GITEA__mailer__USER: "noreply@example.com"
  GITEA__mailer__PASSWD: "changeme"
```
Full list of available configuration options is listed on [Gitea's Documentation site](https://docs.gitea.io/en-us/config-cheat-sheet/).  

Useful guide about how to map settings to environment variable names -  https://github.com/go-gitea/gitea/tree/main/contrib/environment-to-ini

# Example Playbook

```
- hosts:
    - gitea.example.com
  become: yes
  roles:
    - role: almaops.ct_gitea
      gitea_domain: gitea.example.com
      ct_gitea_env_app_name: "Gitea"
      ct_gitea_env_extra:
        GITEA__server__DOMAIN: "{{ gitea_domain }}"
        GITEA__server__ROOT_URL: "https://{{ gitea_domain }}/"
        GITEA__server__LOCAL_ROOT_URL: "http://localhost:3000/"
        GITEA__server__SSH_DOMAIN: "{{ gitea_domain }}"
        GITEA__mailer__ENABLED: true
        GITEA__mailer__FROM: "Gitea <noreply@example.com>"
        GITEA__mailer__MAILER_TYPE: smtp
        GITEA__mailer__HOST: "mail.example:587"
        GITEA__mailer__USER: "noreply@example.com"
        GITEA__mailer__PASSWD: "changeme"
```

# License

[MIT License](./LICENSE)

# Author
This role was created by [Valentin Gostev](https://github.com/ussrlongbow)
