# Alpine Images with BASH for use with Ansible Molecule

[![Build Status](https://travis-ci.org/coglinev3/container-alpine-bash.svg?branch=master)](https://travis-ci.org/coglinev3/container-alpine-bash)


In order to test [Ansible](https://docs.ansible.com/ansible/latest/index.html "Ansible Documentation") roles with the help of [Ansible Molecule](https://molecule.readthedocs.io/en/latest/ "Ansible Molecule Documentation"), Docker images are required that contain a bash shell. By default, Alpine Images only contain the Bourne shell `sh`. That's why the standard Alpine Images have been expanded to include the Bourne-again shell `bash`.

These Alpine images were built exclusively with Ansible without the use of Dockerfiles, see the example Ansible playbook below. 

## Tags

  - `3.12`, `latest`: Alpine Linux version 3.12
  - `3.11`: Alpine Linux version 3.11
  - `3.10`: Alpine Linux version 3.10
  - `3.9`: Alpine Linux version 3.9

## Requirements

Ansible and Docker must be installed.

## Example

```yml
---
- hosts: localhost
  connection: local
  gather_facts: no
  vars:
    base_image: alpine
    base_image_tag: "3.11"
    container_name: alpine_311_bash
    image_namespace: coglinev3
    image_name: alpine
    image_tag: 3.11-bash
  pre_tasks:
    - name: Make the latest version of the base image available locally.
      docker_image:
        name: '{{ base_image }}:{{ base_image_tag }}'
        source: pull
        force_source: true
    - name: Create the Docker container.
      docker_container:
        image: '{{ base_image }}:{{ base_image_tag }}'
        name: '{{ container_name }}'
        command: sleep 1h
    - name: Add the newly created container to the inventory.
      add_host:
        hostname: '{{ container_name }}'
        ansible_connection: docker
  tasks:
    - name: "Install Bash"
      raw: apk add --update-cache bash && rm -rf /var/cache/apk/*
      delegate_to: '{{ container_name }}'
  post_tasks:
    - name: Commit the container.
      command: >
        docker commit
        --author "Cogline.v3"
        {{ container_name }} {{ image_namespace }}/{{ image_name }}:{{ image_tag }}
    - name: Remove the container.
      docker_container:
        name: '{{ container_name }}'
        state: absent
```

> Note: Of course, the playbook shown is more complex than a simple Dockerfile which installs the bash, but it demonstrates that container images can be build with Ansible. Many thanks to [Jeff Geerling](https://www.jeffgeerling.com/) for his introduction to Ansible with Docker in his book [Ansible for DevOps](https://www.jeffgeerling.com/project/ansible-devops).

## How to Build

You can build any of these Alpine Images as follows:

```sh
git clone https://github.com/coglinev3/container-alpine-bash.git
cd container-alpine-bash
ansible-playbook alpine-3.11-bash.yml
```

## Notes

I use these images as base images to build other Alpine Linux images with Python and Ansible to test my Ansible roles with these images.

## Version

Release: 1.2.2

## License

GNU Version 3

## Author

Created in 2020, by Cogline.v3.
