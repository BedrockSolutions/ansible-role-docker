# bedrock.docker

Ansible role that performs several Docker-related functions, 
including installation.

A prettier version of this documentation is located at 
https://bedrocksolutions.github.io/ansible-role-docker

* Commands
  * [download_tls_cert_and_key](#download_tls_cert_and_key)
  * [install](#install)
  * [local_volume_from_device](#local_volume_from_device)
  * [prune_images](#prune_images)
  * [swarm_node](#swarm_node)

## Installation

To use `bedrock.docker` in another role, create the file 
`<role_root>/meta/main.yml` with the following structure:

```yaml
dependencies:
  - name: bedrock.docker
    scm: git
    src: https://github.com/BedrockSolutions/ansible-role-docker.git
    vars:
      docker:
        command: dependency
    version: master
```

If you just want to use this role in a playbook or task file, then
add an entry to the `requirements.yml` file:

```yaml
- name: bedrock.docker
  scm: git
  src: https://github.com/BedrockSolutions/ansible-role-docker.git
  version: master
```
>__Note:__ In both examples, the `version` field can be a branch, tag, or commit hash.

The role can now be imported or included as `bedrock.docker`.

## Command Invocation

The role is made up of commands that can be invoked using the following 
syntax:

```yaml
- import_role
    name: bedrock.docker
  vars:
    docker:
      command: <command_name>
      ...command_vars
```

or

```yaml
- include_role
    name: bedrock.docker
  vars:
    docker:
      command: <command_name>
      ...command_vars
```

## Commands

### __download_tls_cert_and_key__

Fetches a TLS certificate and key, and saves them to the specified
locations.

#### Arguments

* __`cert_path`:__ the path to the saved certificate
    * type: string
    * default: `'/etc/docker/tls_cert.pem'`
  
* __`cert_url`:__ the certificate's URL
    * type: string
    * format: uri
  
* __`key_path`:__ the path to the saved key
    * type: string
    * default: `'/etc/docker/tls_key.pem'`
  
* __`key_url`:__ the key's URL
    * type: string
    * format: uri

#### Example

Download a cert and key, saving them to the default locations:

```yaml
- import_role:
    name: bedrock.docker
  vars:
    docker:
      command: download_tls_cert_and_key
      cert_url: http://foo.com/my_tls_cert.pem
      key_url: http://foo.com/my_tls_key.pem
```
  
### __install__

Installs docker and configures a `systemd` service.

#### Arguments

* __`build`:__ the docker build to install
    * type: string
    * enum: `['edge', 'stable']`
    * default: `'stable'`
  
* __`edition`:__ the docker edition (community or enterprise)
to install
    * type: string
    * enum: `['ce', 'ee']`
    * default: `'ce'`

* __`install_compose`:__ install compose
    * type: boolean
    * default: `false`
  
* __`manipulate_iptables`:__ enable iptables manipulation
    * type: boolean
    * default: `true`

* __`tls_cert_path`:__ path to the TLS certificate
    * type: string
    * default: `'/etc/docker/tls_cert.pem'`
  
* __`tls_enable`:__ enable TLS encryption
    * type: boolean
    * default: `true`
  
* __`tls_key_path`:__ path to the TLS key
    * type: string
    * default: `'/etc/docker/tls_key.pem'`
  
* __`user`__ user to add to the `docker` group
    * type: string
    * default: `'ubuntu'`

#### Example
    
Install Docker with TLS encryption turned off:

```yaml
- import_role:
    name: bedrock.docker
  vars:
    docker:
      command: install
      tls_enable: false
```

### __local_volume_from_device__

Manages a local docker volume created from a device.

#### Arguments

* __`device`__ the device to use as a volume
    * type: string

* __`mount_point`__ an optional secondary mount point for the device
    * type: string
    
* __`name`__ the volume name
    * type: string

#### Example

```yaml
- import_role:
    name: bedrock.docker
  vars:
    docker:
      command: local_volume_from_device
      device: /dev/sdb
      mount_point: /mnt/docker_volumes/factom_database
      name: factom_database
```

### __prune_images__

Prunes all unused images

#### Example

```yaml
- import_role:
    name: bedrock.docker
  vars:
    docker:
      command: prune_images
```

### __swarm_worker_node__

Manages swarm membership for a worker node

#### Arguments

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
    name: bedrock.docker
  vars:
    docker:
      command: swarm_node
      manager_host: 1.2.3.4
      token: "{{ my_swarm_token }}"
```
