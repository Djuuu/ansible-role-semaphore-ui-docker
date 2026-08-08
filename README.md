Ansible Role: Semaphore-UI-docker
=================================

Install Semaphore UI Docker Compose project.

- https://semaphoreui.com/
- https://github.com/semaphoreui/semaphore

Uses custom image to change the user running Semaphore UI.

Requirements
------------

Requires the following to be installed:
- docker
- docker compose

Role Variables
--------------

Common Docker projects variables:

```yaml
# Base directory for Docker projects
docker_projects_path: # /var/apps

# Base domain suffix through which service is accessible internally
local_base_domain: my-host.example.net
```

Available role variables are listed below, along with default values (see `defaults/main.yml`):

```yaml
# Docker project variables

semaphore_project_name: semaphore

# Docker project dynamic vars (uses `docker_project_name` prefix, adapt if overridden)

# Additional external docker-compose networks, joined by main service
semaphore_additional_networks: []
#  - example_default

semaphore_traefik_loadbalancer_server_port: 3000
semaphore_traefik_entrypoints: http,https
semaphore_traefik_middlewares:
  - "internal-access@file"

# Main service additional docker-compose options (ex: cpu_shares, deploy, ...)
semaphore_service_additional_options: |
  #ports:
  #  - 3000:3000

# MySQL service additional docker-compose labels (ex: wud.tag.include, ...)
semaphore_mysql_service_additional_labels: ""
# PostgreSQL service additional docker-compose labels (ex: wud.tag.include, ...)
semaphore_postgres_service_additional_labels: ""
```

```yaml
# Semaphore project variables

# semaphoreui/semaphore image version
semaphore_version: latest

# UID container is running as
semaphore_puid: "{{ ansible_facts['user_uid'] }}"
# GID container is running as
semaphore_pgid: "{{ ansible_facts['user_gid'] }}"
# System timezone
semaphore_timezone: UTC

# Required Python modules (note: ansible is included by default)
semaphore_python_requirements: []

# Ansible playbooks mounted in Semaphore UI
semaphore_playbook_mounts: []
#  # Mount current playbook
#  - src: "{{ playbook_dir }}"
#    dest: "/home/semaphore/{{ playbook_dir | basename }}"

# Mysql image version (when semaphore_db_dialect = mysql)
semaphore_mysql_version: 8.4
# Postgres image version (when semaphore_db_dialect = postgres)
semaphore_postgres_version: 18
```

```yaml
# Administrator account
semaphore_admin_name:     Admin
semaphore_admin_email:    admin@example.net
semaphore_admin:          admin
semaphore_admin_password: changeme

# Database
semaphore_db_dialect: postgres  # postgres | mysql | sqlite
semaphore_db_host:    postgres
semaphore_db_name:    semaphore
semaphore_db_user:    semaphore
semaphore_db_pass:    semaphore
semaphore_db_port:    5432      # 5432 | 3306
semaphore_db_options:
  sslmode: disable

# Should Ansible check host keys when connecting
semaphore_ansible_host_key_checking: false

# Security
# Generate key:
#   head -c32 /dev/urandom | base64
# Rekey database secrets:
#   semaphore --config /etc/semaphore/config.json vault rekey --old-key <previous-encryption-key>
semaphore_access_key_encryption: "gs72mPntFATGJs9qK0pQ0rKtfidlexiMjYCH9gWKhTU="
#semaphore_option_encryption:
#semaphore_cookie_hash:
#semaphore_cookie_encryption:

semaphore_schedule_timezone: "{{ semaphore_timezone }}"

# environment variables which will be available for apps (Ansible, Terraform, etc).
#semaphore_env_vars: {}

# environment variables which will be forwarded from system
semaphore_forwarded_env_vars:
  - ANSIBLE_HOST_KEY_CHECKING
```

```yaml
# Semaphore variables (backup / restore)

# Semaphore UI API base URL
semaphore_api_base_url: "https://{{ docker_project_slug }}.{{ exposed_base_domain | default(local_base_domain, true) }}/api"

# Semaphore UI API token (will be generated through admin authentication if empty)
semaphore_api_token:

# Check Semaphore UI certificates
semaphore_api_validate_certs: true

# Location of current (latest) backup file
semaphore_config_path: "{{ playbook_dir }}/config/semaphore"
# Location of archived (previous) backup files
semaphore_backup_path: "{{ playbook_dir }}/config/semaphore/backup"

# Backup file options
semaphore_backup_owner:
semaphore_backup_group:
semaphore_backup_mode:
# Can be useful to force to true when destination filesystem is not posix-compatible (ex: SMB share)
semaphore_backup_become: false
```

```yaml
# Additional Semaphore configuration variables

#semaphore_git_client:            cmd_git # cmd_git | go_git
#semaphore_ssh_path:              ~/.ssh/config.
#semaphore_port:                  3000
#semaphore_interface:

#semaphore_tmp_path:              /tmp/semaphore
#semaphore_secrets_path:          /tmp/semaphore
#semaphore_repos_dir:
#semaphore_ssh_agent_sockets_dir: /tmp/semaphore
#semaphore_home_dir_mode:         template_dir # template_dir | project_home | user_home

#semaphore_max_parallel_tasks:    9999
#semaphore_max_task_duration_sec:
#semaphore_max_tasks_per_template:

#semaphore_oidc_providers: >-
#  '{
#    "github": {
#      "client_id": "***",
#      "client_secret": "***",
#      // ...
#    }
#  }'

#semaphore_password_login_disabled:
#semaphore_non_admin_can_create_project:

#semaphore_apps: {}

#semaphore_use_remote_runner:
#semaphore_runner_registration_token:

## JWT
#semaphore_jwt_enabled:
#semaphore_jwt_issuer:
#semaphore_jwt_default_ttl: 1h
#semaphore_jwt_max_ttl: 24h

## Runner

## Runners (server-side fleet)

## Teams
#semaphore_teams_invites_enabled:
#semaphore_teams_invite_type: username # username | email | both
#semaphore_teams_members_can_leave:

## Security

#semaphore_web_root:

#semaphore_tls_enabled:
#semaphore_tls_cert_file:
#semaphore_tls_key_file:
#semaphore_tls_http_redirect_addr:
#semaphore_tls_http_redirect_port:

#semaphore_totp_enabled:
#semaphore_totp_issuer:
#semaphore_totp_allow_recovery:

#semaphore_email_2tp_enabled:
#semaphore_email_2tp_allow_login_as_external_user:
#semaphore_email_2tp_allow_create_external_user:
#semaphore_email_2tp_allowed_domains: []
#semaphore_email_2tp_disable_for_oidc:

## Metrics
#semaphore_metrics_enabled:
#semaphore_metrics_username:
#semaphore_metrics_password:

## Encryption
#semaphore_encryption_keys_file:
#semaphore_encryption_keys_poll_interval:

## Process
#semaphore_process_user:
#semaphore_process_uid:
#semaphore_process_gid:
#semaphore_process_chroot:
#semaphore_process_no_new_privs:
#semaphore_process_app_ns_user:
#semaphore_process_app_ns_mount:
#semaphore_process_app_ns_pid:
#semaphore_process_app_ns_ipc:
#semaphore_process_app_ns_uts:

## Email
#semaphore_email_sender:
#semaphore_email_host:
#semaphore_email_port:
#semaphore_email_secure:
#semaphore_email_tls:
#semaphore_email_tls_min_version:
#semaphore_email_username:
#semaphore_email_password:
#semaphore_email_alert:

## Messengers
#semaphore_telegram_alert:
#semaphore_telegram_chat:
#semaphore_telegram_token:
#semaphore_slack_alert:
#semaphore_slack_url:
#semaphore_microsoft_teams_alert:
#semaphore_microsoft_teams_url:
#semaphore_rocketchat_alert:
#semaphore_rocketchat_url:
#semaphore_dingtalk_alert:
#semaphore_dingtalk_url:
#semaphore_gotify_alert:
#semaphore_gotify_url:
#semaphore_gotify_token:

## LDAP
#semaphore_ldap_enable:
#semaphore_ldap_needtls:
#semaphore_ldap_bind_dn:
#semaphore_ldap_bind_password:
#semaphore_ldap_server:
#semaphore_ldap_search_dn:
#semaphore_ldap_search_filter:
#semaphore_ldap_mapping_dn:
#semaphore_ldap_mapping_mail:
#semaphore_ldap_mapping_uid:
#semaphore_ldap_mapping_cn:

## Logging
#semaphore_log_level:
#semaphore_debug_filter:
```

Dependencies
------------

This role depends on :
- [djuuu.docker_project](https://github.com/Djuuu/ansible-role-docker-project)

Some variables allow integration with:
- [djuuu.traefik_docker](https://github.com/Djuuu/ansible-role-traefik-docker)

Example Playbook
----------------

Install Semaphore UI:
```yaml
- hosts: all
  gather_facts: true
  gather_subset:
    - "!all"
    - "!min"
    - user_id

  roles:
    - djuuu.semaphore_ui_docker
```

Backup projects:
```yaml
- hosts: all
  gather_facts: true
  gather_subset:
    - "!all"
    - "!min"
    - user_id
  
  tasks:

    - name: Local backup to playbook configuration
      when: local | default(false) | bool
      ansible.builtin.set_fact:
        semaphore_config_path: "{{ playbook_dir }}/config/semaphore"
        semaphore_backup_path: "{{ playbook_dir }}/config/semaphore/backup"

    - name: Backup Semaphore UI projects
      ansible.builtin.include_role:
        name: djuuu.semaphore_ui_docker
        tasks_from: backup
```

Restore projects:
```yaml
- hosts: all
  gather_facts: false

  tasks:
    - name: Restore Semaphore UI projects
      ansible.builtin.include_role:
        name: djuuu.semaphore_ui_docker
        tasks_from: restore
```

License
-------

Beerware License
