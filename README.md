Description
=========
This project provides turnkey dockerize web application using nginx. You can create, build and publish a webserver from scratch with the following `Infrastructure as Code` practices:

**Project written by Ansible and will do for you:**
- Install docker and docker-compose with dependecies on your machine such as local, cloud, datacenter
- Clone your application source code from SCM 
- Build image using `docker-compose` and run the application as a docker container

Is this project suitable for Production?
=========

This project has been created for only test purposes. Production envrionment should have `high availability`, `scalability`, `load balancing`, `CI-CD pipeline`, `logging`, `monitoring` and `security systems` etc.

Why did I choose Ansible and Docker-compose
=========

- Ansible is a configuration management tool that designed to install and configure software on existing servers.

- Ansible does allow you to use a specific host or tags to search for existing EC2 instances before deploying the new ones.

- Ansible works by connecting directly to each server over SSH. Just use your SSH keys.

- This project uses the only one docker container each machine but we need to update docker image and restart the container once updated source code. 

- Docker cannot manage these operations basically, you need a few complicate steps but docker-compose can handle with a single command like `docker-compose up` without any other operations.


Roles
=========
Roles are ways of the load automatically the certain `vars_files`, `tasks`, and `handlers` based on a known file structure. Grouping content by roles also allows easy sharing of roles with other users.

The project has 2 main roles:

- **docker-setup:** This role install docker, docker-compose.
- **webserver:** This role copies the server configs, build docker image and run webserver as a docker container.

Requirements
------------
- **Python (2.6 or later) or (3.5 or later):** https://www.python.org/downloads/
- **Ansible-Playbook 2.8.6:** https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html#installing-the-control-node

*If you want to use a dynamic inventory for AWS*
- **boto:** https://pypi.org/project/boto/
- **aws-cli:** https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-install.html

Project Structure
--------------
```
├── README.md
├── ansible.cfg
├── inventory
│   ├── ec2.ini
│   ├── ec2.py
│   └── hosts
├── roles
│   ├── docker-setup
│   │   ├── defaults
│   │   │   └── main.yml
│   │   └── tasks
│   │       └── main.yml
│   └── webserver
│       ├── files
│       │   └── web-files
│       │       ├── Dockerfile
│       │       ├── docker-compose.yaml
│       │       └── nginx
│       │           ├── default.conf
│       │           └── nginx.conf
│       └── tasks
│           └── main.yaml
└── setup.yaml
```

Role Variables
--------------

`docker-setup` role's default variables listed below. You can change these variables if needed.
- **docker_user:** `"ec2-user"`
- **docker_compose_version:** `1.24.1`
- **docker_compose_path:** `"/usr/local/bin/docker-compose"`


Hosts Variables and Tags
--------------
- setup.yaml file has `{{ host_tag }}` variable. You can use this variable during the playbook runtime like `-e "host_tag=tag_Name_webapp_1"`. _Detailed usage described example section._

- Ansible roles defined with tags for elasticity. For example, if you want to use specific roles such as `docker-setup` like this `--tags="docker-setup"` during the playbook runtime, Ansible will play only `docker-setup` role for selected hosts. _Detailed usage described below section._


Example Playbook
----------------

- **Static host usage:**
```
ansible-playbook -i inventory/hosts setup.yaml -e "host_tag=all"
ansible-playbook -i inventory/hosts setup.yaml -e "host_tag=web_server_1"
```
- **Dynamic host usage:** tag_\<key>_\<value>
```
ansible-playbook -i inventory/ec2.py setup.yaml -e "host_tag=tag_Name_webapp_1"
ansible-playbook -i inventory/ec2.py setup.yaml -e "host_tag=tag_Name_webapp"
```
- **Specific role usage:**
```
ansible-playbook -i inventory/ec2.py setup.yaml -e "host_tag=tag_Name_webapp_1" --tags="webserver"
```
Sample Usage
----------------

- **Step 1:** Define your `SSH key` and `remote user` into the `ansible.cfg`

- **Step 2:** Define your application code repository into the `Dockerfile`
```
    roles/webserver/files/Dockerfile
```

- **Step 3:** Deploy the application
```
    ansible-playbook -i inventory/hosts setup.yaml -e "host_tag=all"
```
- **Step 4:** To update, push your changes to the repository and repeat `Step 3` for deploying the application to the instances.


License
-------

Apache License 2.0
