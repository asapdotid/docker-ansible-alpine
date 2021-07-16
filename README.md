# Docker-Ansible base image

![Docker Automated build](https://img.shields.io/docker/automated/asapdotid/ansible-alpine) [![Docker Pulls](https://img.shields.io/docker/pulls/asapdotid/ansible-alpine.svg)](https://hub.docker.com/r/asapdotid/ansible-alpine/)

## Additional services

- Sed
- Bash
- Base64

## Usage

### Environnement variable

| Variable             | Default Value    | Usage                                       |
| -------------------- | ---------------- | ------------------------------------------- |
| PIP_REQUIREMENTS     | requirements.txt | install python library requirements         |
| ANSIBLE_REQUIREMENTS | requirements.yml | install ansible galaxy roles requirements   |
| DEPLOY_KEY           |                  | pass an SSH private key to use in container |
| DEPLOY_HOST          |                  | pass an SSH known hosts use in container    |

### Mitogen

To enable mitogen, add this configuration into defaults in defaults.cfg file

```
action_plugins = ~/.ansible/plugins/action:/usr/share/ansible/plugins/action
strategy_plugins = /opt/mitogen/ansible_mitogen/plugins/strategy
strategy = mitogen_linear
```

### Run Playbook

```
docker run -it --rm \
  -v ${PWD}:/ansible \
  asapdotid/ansible-alpine:latest \
  ansible-playbook -i inventory playbook.yml
```

### Generate Base Role structure

```
docker run -it --rm \
  -v ${PWD}:/ansible \
  asapdotid/ansible-alpine:latest \
  ansible-galaxy init role-name
```

### Lint Role

```
docker run -it --rm asapdotid/ansible-alpine:latest \
  -v ${PWD}:/ansible ansible-playbook tests/playbook.yml --syntax-check
```

### Run with forwarding ssh agent

```
docker run -it --rm \
  -v $(readlink -f $SSH_AUTH_SOCK):/ssh-agent \
  -v ${PWD}:/ansible \
  -e SSH_AUTH_SOCK=/ssh-agent \
  pad92/ansible-alpine:latest \
  sh
```

### Modified entrypoint & Dockerfile

- insert SSH private key use `base64` decode `-d`
- Dockerfile add ssh config `LogLevel ERROR`

### Ansible Galaxy Collection

- Ansible Synchronize `ansible.posix` (https://galaxy.ansible.com/ansible/posix)
- Ansible Docker `community.docker` (https://galaxy.ansible.com/community/docker)
