# Alpine Images with BASH for use with Ansible Molecule

[![Build](https://github.com/coglinev3/container-alpine-bash/actions/workflows/build.yml/badge.svg)](https://github.com/coglinev3/container-alpine-bash/actions/workflows/build.yml)

In order to test [Ansible](https://docs.ansible.com/ansible/latest/index.html
"Ansible Documentation") roles with the help of [Ansible
Molecule](https://molecule.readthedocs.io/en/latest/ "Ansible Molecule
Documentation"), Docker images are required that contain a bash shell. By
default, Alpine Images only contain the Bourne shell `sh`. That's why the
standard Alpine Images have been expanded to include the Bourne-again shell
`bash`.

These Alpine images were built exclusively with Ansible without the use of
Dockerfiles, see the example Ansible playbook below. 

## Tags

  - `3.19`, `latest`: Alpine Linux version 3.19
  - `3.18`, Alpine Linux version 3.18
  - `3.17`, Alpine Linux version 3.17
  - `3.16`, Alpine Linux version 3.16
  - `3.15`, Alpine Linux version 3.15
  - `3.14`, Alpine Linux version 3.14
  - `3.13`, Alpine Linux version 3.13
  - `3.12`: Alpine Linux version 3.12

## Requirements

Ansible and Docker must be installed.

## Ansible Playbook

```yml
---
- name: Create Alpine Linux container
  hosts: localhost
  connection: local
  gather_facts: false
  vars:
    alpine_version: "{{ lookup('env','ALPINE_VERSION') | default('3.19', true) }}"
    base_image: alpine
    container_name: "alpine_bash"
    image_namespace: coglinev3
    image_name: alpine
  pre_tasks:
    - name: Make the latest version of the base image available locally.
      community.docker.docker_image:
        name: '{{ base_image }}:{{ alpine_version }}'
        source: pull
        force_source: true
    - name: Create the Docker container.
      community.docker.docker_container:
        image: '{{ base_image }}:{{ alpine_version }}'
        name: '{{ container_name }}'
        container_default_behavior: no_defaults
        command: sleep 1h
    - name: Add the newly created container to the inventory.
      ansible.builtin.add_host:
        hostname: '{{ container_name }}'
        ansible_connection: docker
  tasks:
    - name: "Install Bash"
      ansible.builtin.raw: apk add --update-cache bash && rm -rf /var/cache/apk/*
      args:
        creates: /bin/bash
      delegate_to: '{{ container_name }}'
  post_tasks:
    - name: Commit the container.
      ansible.builtin.command: >
        docker commit
        --author "Cogline.v3"
        {{ container_name }} {{ image_namespace }}/{{ image_name }}:{{ alpine_version }}
      register: docker_commit
      changed_when: docker_commit.rc == 0
      failed_when: docker_commit.rc != 0
    - name: Remove the container.
      community.docker.docker_container:
        name: '{{ container_name }}'
        state: absent
        container_default_behavior: no_defaults
```

!!! note

      Of course, this playbook is more complex than a simple Dockerfile that
      installs the Bash package, but it shows that it is possible to create
      container images with Ansible. Many thanks to [Jeff
      Geerling](https://www.jeffgeerling.com/) for his introduction to Ansible
      with Docker in his book [Ansible for
      DevOps](https://www.jeffgeerling.com/ project/ansible-devops).
## How to Build

You can build any of these Alpine Images as follows:

```sh
git clone https://github.com/coglinev3/container-alpine-bash.git
cd container-alpine-bash
ALPINE_VERSION=3.17 ansible-playbook container-alpine.yml
```

## Notes

I use these images as base images to build other Alpine Linux images with Python and Ansible to test my Ansible roles with these images.

## Version

Release: 1.10.0

## License

GNU GPL Version 3

## Author

Copyright &copy; 2024 Cogline.v3.
