# jcheroske.ansible_role_docker

Ansible role that performs several docker-related functions, 
including installation and firewall configuration.

## Dependencies

Add `angstwad.docker_ubuntu` to `requirements.yml`:
```yaml
- src: angstwad.docker_ubuntu
```

`ufw` firewall:
```bash
sudo apt install ufw
```

## Installation

### Client role

In a client role, create the file `./meta/main.yml` with the 
following structure:

```yaml

---
dependencies:
  - { role: jcheroske.ansible_role_docker }
```

### Direct playbook usage

Add the role to `requirements.yml`:

```yaml
- src: jcheroske.ansible_role_docker
```

## Usage

The role is invoked by importing/including the role, specifying a 
command and passing associated variables, all within the `docker`
namespace.

```yaml
- import_role:
    name: jcheroske.ansible_role_docker
  vars:
    docker:
      command: some_command
      foo: true
      bar: "blah"

```

### Commands

#### install

Completely installs docker

##### Variables

* `build`: the docker build to install
  * type: `string`
  * enum: `stable` or `edge`
  * default: `stable`
  * required: `yes`
  
* `tls_cert_url`: url to a docker daemon tls certificate
  * type: `string`
  * format: `uri`
  * required: `no`

* `tls_key_url`: url to a docker daemon tls key
  * type: `string`
  * format: `uri`
  * required: `no`

* `uninstall_previous_versions`: should previous installations of
docker be unintalled?
  * type: `boolean`
  * default: `true`
  * required: `yes`

* `user`: user to add to the `docker` group
  * type: `string`
  * default: ubuntu
  * required: `yes`

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