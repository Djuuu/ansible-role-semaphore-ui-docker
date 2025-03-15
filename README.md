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

semaphore_traefik_loadbalancer_server_port: 3000
semaphore_traefik_entrypoints: http,https
semaphore_traefik_middlewares:
  - "internal-access@file"

# Main service additional docker-compose options (ex: cpu_shares, deploy, ...)
semaphore_compose_service_additional_options: |
  #ports:
  #  - 3000:3000
```

```yaml
# Semaphore project variables

# semaphoreui/semaphore image version
semaphore_version: latest

# Semaphore network mode (bridge|host)
semaphore_network_mode: bridge

# Additional external docker-compose networks (bridge network mode)
semaphore_compose_additional_networks: []
#  - example_default

# Required Python modules (note: ansible is included by default)
semaphore_python_requirements: []

# Ansible playbooks mounted in Semaphore UI
semaphore_playbook_mounts: []
#  # Mount current playbook
#  - src: "{{ playbook_dir }}"
#    dest: "/home/semaphore/{{ playbook_dir | basename }}"
```

```yaml
# Semaphore variables

#semaphore_port: 3000
#semaphore_interface:
#semaphore_tmp_path: /tmp/semaphore
#semaphore_git_client: cmd_git
#semaphore_web_root:

semaphore_db_dialect: postgres # postgres|mysql|bolt
semaphore_db_host:    postgres
semaphore_db_port:    5432     # 5432|3306|

semaphore_db:      semaphore
semaphore_db_user: semaphore
semaphore_db_pass: semaphore

#semaphore_cookie_hash:
#semaphore_cookie_encryption:

# Generate key:
#   head -c32 /dev/urandom | base64
# Rekey database secrets:
#   semaphore --config /etc/semaphore/config.json vault rekey --old-key <previous-encryption-key>
semaphore_access_key_encryption: "gs72mPntFATGJs9qK0pQ0rKtfidlexiMjYCH9gWKhTU="

# Note: there are some timezone bugs:
# - https://github.com/semaphoreui/semaphore/issues/1469
# - https://github.com/semaphoreui/semaphore/issues/974
semaphore_timezone: UTC

# Administrator account
semaphore_admin_name:     Admin
semaphore_admin_email:    admin@example.net
semaphore_admin:          admin
semaphore_admin_password: changeme

# Should Ansible check host keys when connecting
semaphore_ansible_host_key_checking: false

# environment variables which will be available for apps (Ansible, Terraform, etc).
semaphore_env_vars: {}
#  VAR_NAME: value

# environment variables which will be forwarded from system
semaphore_forwarded_env_vars:
  - ANSIBLE_HOST_KEY_CHECKING
```

```yaml
# Semaphore variables (backup)

# Semaphore UI API base URL
semaphore_api_base_url: "https://{{ docker_project_slug }}.{{ local_base_domain }}/api"
# Check Semaphore UI certificates
semaphore_api_validate_certs: true

# Location of current (latest) backup file
semaphore_config_path: "{{ playbook_dir }}/config/semaphore"
# Location of archived (previous) backup files
semaphore_backup_path: "{{ playbook_dir }}/config/semaphore/backup"
```

```yaml
# Semaphore variables (optional)

#semaphore_max_task_duration_sec:
#semaphore_max_tasks_per_template:
#semaphore_max_parallel_tasks:

#semaphore_password_login_disabled:
#semaphore_non_admin_can_create_project:
#semaphore_use_remote_runner:

#semaphore_ldap_activated:     'no'
#semaphore_ldap_host:
#semaphore_ldap_port:
#semaphore_ldap_needtls:
#semaphore_ldap_dn_bind:
#semaphore_ldap_password:
#semaphore_ldap_dn_search:
#semaphore_ldap_search_filter:

#semaphore_email_alert:
#semaphore_email_sender:
#semaphore_email_host:
#semaphore_email_port:
#semaphore_email_username:
#semaphore_email_password:
#semaphore_email_secure:

#semaphore_telegram_alert:
#semaphore_telegram_chat:
#semaphore_telegram_token:
#semaphore_slack_alert:
#semaphore_slack_url:
#semaphore_rocketchat_alert:
#semaphore_rocketchat_url:
#semaphore_microsoft_teams_alert:
#semaphore_microsoft_teams_url:
#semaphore_dingtalk_alert:
#semaphore_dingtalk_url:
#semaphore_gotify_alert:
#semaphore_gotify_url:
#semaphore_gotify_token:
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
  gather_facts: false
  
  tasks:
    - name: Backup Semaphore UI projects
      ansible.builtin.include_role:
        name: djuuu.semaphore_ui_docker
        tasks_from: backup
```

License
-------

Beerware License
