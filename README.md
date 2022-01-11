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

---
## Variables Used

```
    git_url: https://github.com/Jisjo/Docker_python_flask_helow-world.git     # Git repo URL
    docker_user: "jisjo"                                                      # DockerHub username
    docker_password: "****************"                                       # DockerHub password
    image_name: "jisjo/devops-flaskapp"                                       # Image name
```

---

## Provisioning
