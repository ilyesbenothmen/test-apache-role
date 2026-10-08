# Test Apache Role

Ansible project that installs and configures Apache on an Ubuntu web server.

## Target host

- Group: `webservers`
- Host: `172.31.128.20`
- SSH user: `ubuntu`

## Project structure

```text
.
├── ansible.cfg
├── inventory/
│   └── ci.yml
├── playbooks/
│   └── play.yml
├── roles/
│   └── apache/
└── requirements.yml
```

## Role

The local `apache` role is based on the Ansible Galaxy role `geerlingguy.apache`.

## Run the playbook

```bash
ansible-playbook playbooks/play.yml
```

## Idempotency validation

Run the playbook twice:

```bash
ansible-playbook playbooks/play.yml
ansible-playbook playbooks/play.yml
```

The second run should report:

```text
changed=0
failed=0
unreachable=0
```

## Lint check

```bash
ansible-lint -t idempotency playbooks/play.yml
```
