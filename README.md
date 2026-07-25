# Ansible

Ansible is an open-source automation tool used for **configuration management**, **application deployment**, **orchestration**, and **provisioning**. It lets you describe your infrastructure as code and apply changes consistently across many machines.

## Why Ansible?

- **Agentless** — connects over SSH (or WinRM for Windows), so there's nothing to install on managed nodes.
- **Simple syntax** — playbooks are written in YAML, which is human-readable and easy to version control.
- **Idempotent** — running the same playbook multiple times produces the same result without unwanted side effects.
- **Extensible** — thousands of modules and community roles via Ansible Galaxy.
- **Push-based** — changes are pushed from a control node rather than pulled by agents.

## Core Concepts

| Concept | Description |
|---|---|
| **Inventory** | A file (INI or YAML) listing the hosts/groups Ansible manages. |
| **Playbook** | A YAML file defining a set of tasks to run against hosts. |
| **Task** | A single action, usually invoking a module (e.g., install a package). |
| **Module** | A unit of code Ansible executes (e.g., `apt`, `copy`, `service`). |
| **Role** | A reusable, structured collection of tasks, handlers, files, and templates. |
| **Handler** | A task triggered only when notified by another task (e.g., restart a service after a config change). |
| **Variable** | Data used to customize behavior across hosts/environments. |
| **Facts** | System information automatically gathered from managed nodes. |

## Installation

```bash
# Debian/Ubuntu
sudo apt update && sudo apt install ansible -y

# RHEL/CentOS/Fedora
sudo dnf install ansible -y

# via pip
pip install ansible
```

Verify installation:

```bash
ansible --version
```

## Example Inventory

```ini
[webservers]
web1.example.com
web2.example.com

[dbservers]
db1.example.com

[all:vars]
ansible_user=deploy
```

## Example Playbook

```yaml
---
- name: Configure web servers
  hosts: webservers
  become: true

  tasks:
    - name: Install nginx
      apt:
        name: nginx
        state: present
        update_cache: true

    - name: Copy nginx config
      copy:
        src: files/nginx.conf
        dest: /etc/nginx/nginx.conf
      notify: Restart nginx

  handlers:
    - name: Restart nginx
      service:
        name: nginx
        state: restarted
```

Run it with:

```bash
ansible-playbook -i inventory.ini site.yml
```

## Useful Ad-Hoc Commands

```bash
# Check connectivity to all hosts
ansible all -i inventory.ini -m ping

# Run a shell command on a group
ansible webservers -i inventory.ini -a "uptime"

# Install a package on all hosts
ansible all -i inventory.ini -b -m apt -a "name=curl state=present"
```

## Recommended Project Structure

```
.
├── inventory/
│   ├── production
│   └── staging
├── group_vars/
│   └── all.yml
├── roles/
│   └── webserver/
│       ├── tasks/
│       ├── handlers/
│       ├── templates/
│       ├── files/
│       └── defaults/
├── site.yml
└── ansible.cfg
```

## Best Practices

- Keep secrets encrypted with **Ansible Vault** (`ansible-vault encrypt secrets.yml`).
- Use **roles** to organize reusable logic instead of one giant playbook.
- Pin module and collection versions for reproducibility.
- Use `--check` (dry run) and `--diff` before applying changes in production.
- Store inventories per environment (`production`, `staging`, `dev`).
- Lint playbooks with [`ansible-lint`](https://github.com/ansible/ansible-lint).

## Resources

- [Official Documentation](https://docs.ansible.com/)
- [Ansible Galaxy](https://galaxy.ansible.com/) — community roles and collections
- [GitHub Repository](https://github.com/ansible/ansible)

## License

This document is provided for educational purposes. Ansible itself is licensed under the GNU General Public License v3.0.
