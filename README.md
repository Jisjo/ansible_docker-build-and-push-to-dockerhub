# Docker Image Build and Push the Image to DockerHub using Ansible

[![Build Status](https://travis-ci.org/joemccann/dillinger.svg?branch=master)](https://travis-ci.org/joemccann/dillinger)

----
## Description
Here I'm automating docker image creating using ansible. we will be installing docker and building a docker image for an Flask application.
Also, the image built will be pushed to DockerHub using ansible playbook.


----

## Architecture

![Images](https://github.com/Jisjo/ansible_docker-build-and-push-to-dockerhub/blob/main/Diagram-ansible-1.png)

---

## Overview

In this demo, we will be provisioning the following :

- Install packages: git, docker and its required dependencies to run the playbook on build server.
- Clone the git repository to remote build server.
- Build and push the image created image to DockerHub after login to the dockerHub.
---

## Requirements

- Install Ansible on your machine (ansible master).
- DockerHub account.


### Packages 
```sh
amazon-linux-extras install -y ansbile2
yum install -y git
```

##### Ansible Modules used
- [yum](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/yum_module.html) 
- [pip](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/pip_module.html)
- [service](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/service_module.html)
- [git](https://docs.ansible.com/ansible/latest/collections/ansible/builtin/git_module.html)
- [docker_image](https://docs.ansible.com/ansible/2.8/modules/docker_image_module.html)
- [docker_login](https://docs.ansible.com/ansible/2.9/modules/docker_login_module.html)
----

## How to use
```sh
git clone https://github.com/Jisjo/ansible_docker-build-and-push-to-dockerhub.git
cd ansible_docker-build-and-push-to-dockerhub
```
```sh
ansible-playbook -i inventory 01-docker-build-push.yml
```

----

## Variables Used

```
    git_url: https://github.com/Jisjo/Docker_python_flask_helow-world.git     # Git repo URL
    docker_user: "jisjo"                                                      # DockerHub username
    docker_password: "****************"                                       # DockerHub password
    image_name: "jisjo/devops-flaskapp"                                       # Image name
```

---
## Behind the code

Here we are installing packages to bulder server.

```yml

    - name: "Build - Package Installation"
      yum:
        name:
          - docker
          - git
          - python2-pip
        state: present
            
    - name: "Build - Sarting Docker Service"
      service:
        name: docker
        state: restarted
        enabled: true
            
    - name: "Build - Installing Docker Client For Python"
      pip:
        name: docker-py
        state: present
 ```
 
 Here we clone the Docker file from the github repo.
 ```yml
    - name: "Build - Cloning Github Repository {{ git_url }}"
      git:
        repo: "{{ git_url }}"
        dest: "/var/flaskapp/"
      register: git_status
```

It will login to docker hub and build the image. After bulding the image will push to dockerhub. The below steps only run when there is any chnages in github repo.
```yml
    - name: "Build - Login to remote Repository"
      when: git_status.changed == true
      docker_login:
        username: "{{ docker_user }}"
        password: "{{ docker_password }}"

    - name: "Build - Docker image from Dockerfile"
      when: git_status.changed == true
      docker_image:
        build:
          path: "/var/flaskapp/"
        name: "{{image_name}}"
        tag: "{{ item }}"
        push: yes
        source: build
      with_items:
        - "latest"
        - "{{ git_status.after }}"
```
> Here git repo commited id used as image tag in docker hub
---
## Result
The docker hub will look like below after pushing image.
![image](https://github.com/Jisjo/ansible_docker-build-and-push-to-dockerhub/blob/main/Diagram-ansible-2.png)
