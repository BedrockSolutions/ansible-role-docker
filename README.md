# jcheroske.docker

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

### `download_tls_cert_and_key`

Fetches a TLS certificate and key, and saves them to `/etc/docker`.

#### Variables

* __`cert_url`:__ the certificate's URL
    * type: string
    * saved to `/etc/docker/tls_cert.pem`
  
* __`key_url`:__ the key's URL
    * type: string
    * saved to `/etc/docker/tls_key.pem`

#### Example

```yaml
- import_role:
    name: jcheroske.docker
  vars:
    docker:
      command: download_tls_cert_and_key
      cert_url: http://foo.com/my_tls_cert.pem
      key_url: http://foo.com/my_tls_key.pem
```
  
### `install`

Installs docker and configures a `systemd` service.

#### Variables

* __`build`:__ the docker build to install
    * type: string
    * enum: `['edge', 'stable']`
    * default: `'stable'`
  
* __`edition`:__ the docker edition (community or enterprise)
to install
    * type: string
    * enum: `['ce', 'ee']`
    * default: `'ce'`

* __`enable_tls`:__ enable TLS encryption
    * type: boolean
    * default: `true`
    * expects the following files:
        * `/etc/docker/tls_cert.pem`
        * `/etc/docker/tls_key.pem`
  
* __`install_compose`:__ install compose
    * type: boolean
    * default: `false`
  
* __`manipulate_iptables`:__ enable iptables manipulation
    * type: boolean
    * default: `true`

* __`user`__ user to add to the `docker` group
    * type: string
    * default: `'ubuntu'`

#### Example
    
```yaml
- import_role:
    name: jcheroske.docker
  vars:
    docker:
      command: install
```

### `local_volume_from_device`

Manages a local docker volume created from a device.

#### Variables

* __`device`__ the device to use
    * type: string

* __`mount_point`__ an optional mount point for the device
    * type: string
    
* __`name`__ the volume name
    * type: string

* __`state`__ create or delete the volume
    * type: string
    * enum: `['absent', 'present']`
    * default: `'present'`

#### Example

```yaml
- import_role:
    name: jcheroske.docker
  vars:
    docker:
      command: local_volume_from_device
      device: /dev/sdb
      name: factom_database
```

### `prune_images`

Prunes all unused images

#### Example

```yaml
- import_role:
    name: jcheroske.docker
  vars:
    docker:
      command: prune_images
```

### `swarm_node`

Manages membership for a swarm node

#### Variables

* __`manager_host`__ the swarm manager host or ip
    * type: string
    * format: hostname

* __`manager_port`__ the swarm manager port
    * type: integer
    * default: `2377`

* __`state`__ join or leave the swarm
    * type: string
    * enum: `['absent', 'present']`
    * default: `'present'`

* __`token`__ the swarm entry token
    * type: string

#### Example

```yaml
- import_role:
    name: jcheroske.docker
  vars:
    docker:
      command: swarm_node
      manager_host: 1.2.3.4
      token: "{{ my_swarm_token }}"
```
