# Alpine Images with BASH for use with Ansible Molecule

[![Build Status](https://travis-ci.org/coglinev3/container-alpine-bash.svg?branch=master)](https://travis-ci.org/coglinev3/container-alpine-bash)


In order to test [Ansible](https://docs.ansible.com/ansible/latest/index.html "Ansible Documentation") roles with the help of [Ansible Molecule](https://molecule.readthedocs.io/en/latest/ "Ansible Molecule Documentation"), Docker images are required that contain a bash shell. By default, Alpine Images only contain the Bourne shell `sh`. That's why the standard Alpine Images have been expanded to include the Bourne-again shell `bash`.

These Alpine images were built exclusively with Ansible without the use of Dockerfiles, see the example Ansible playbook below. 

## Tags

  - `latest`: Latest stable version of Alpine Linux
  - `3.11-bash`: Alpine Linux version 3.11
  - `3.10-bash`: Alpine Linux version 3.10
  - `3.9-bash`: Alpine Linux version 3.9

## Requirements

Ansible and Docker must be installed.

## How to Build

You can build any of these Alpine Images as follows:

```sh
git clone https://github.com/coglinev3/container-alpine-bash.git
cd container-alpine-bash
ansible-playbook alpine-3.11-bash.yml
```

## Notes

I use these images as base images for building images with Python and Ansible inside.

## Author

Created in 2020 by Cogline.v3
