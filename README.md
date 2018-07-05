# jcheroske.ansible_role_docker

Ansible role that performs several docker-related functions, 
including installation.

## Dependencies

`angstwad.docker_ubuntu` from Ansible Galaxy. Add to `requirements.yml`:

```yaml
- src: angstwad.docker_ubuntu
```

## Installation

### When using in another role:

In a client role, create the file `./meta/main.yml` with the 
following structure:

```yaml

---
dependencies:
  - name: jcheroske.docker
    scm: git
    src: git@github.com:jcheroske/ansible-role-docker.git
    vars:
      docker:
        command: dependency
   version: master
```

### When using in a playbook:

Add the role to `requirements.yml`:

```yaml
  - name: jcheroske.docker
    scm: git
    src: git@github.com:jcheroske/ansible-role-docker.git
    version: master
```

## Tasks/Commands

### `install`

Installs docker, docker compose, and configures a `systemd` service.

##### Variables

* `build` __(string)__ the docker build to install
  * enum: `['edge', 'stable']`
  * default: `'stable'`
  
* `tls_cert_url` __(string)?__ url to a docker daemon TLS certificate
  * format: `uri`

* `tls_key_url` __(string)?__ url to a docker daemon TLS key
  * format: `uri`

* `uninstall_previous_versions` __(boolean)__ should previous installations of
docker be uninstalled?
  * default: `true`

* `user` __(string)__ user to add to the `docker` group
  * default: `'ubuntu'`

##### Example
    
```yaml
- import_role:
    name: jcheroske.ansible_role_docker
  vars:
    docker:
      command: install
      tls_cert_url: http://foo.com/docker_cert.pem
      tls_key_url: http://foo.com/docker_key.pem
      user: ubuntu
```