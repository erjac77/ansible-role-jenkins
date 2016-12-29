# ANSIBLE ROLE: JENKINS CI

An Ansible role to install Jenkins CI on various platforms.

_**Note:** At the moment, Docker is the only platform supported by this role._

## REQUIREMENTS

* Docker >= 1.12.4

## INSTALLATION

#### USING _ANSIBLE GALAXY_:

```
ansible-galaxy install erjac77.jenkins
```

#### USING _GIT CLONE_:

Clone this repository inside the `roles/` subdirectory of your playbook or inside one of the additional directories specified by the `roles_path` setting in `ansible.cfg`.

```
git clone https://github.com/erjac77/ansible-role-jenkins.git erjac77.jenkins
```

## ROLE VARIABLES

```
---

# Jenkins host platform
jenkins_platform: Docker
#jenkins_platform: "{{ ansible_os_family }}"

# Gogs application general settings
jenkins_hostname: "{{ hostvars[inventory_hostname]['ansible_default_ipv4']['address'] }}"
jenkins_port: 8080
jenkins_config_path: "{{ role_path }}/files/config.xml"
jenkins_java_opts: "-Djenkins.install.runSetupWizard=false"
jenkins_force_restart: yes

# Jenkins connection timeout settings
jenkins_connection_retries: 60
jenkins_connection_delay: 5

# Jenkins admin account settings
jenkins_admin_username: jenkins
jenkins_admin_password: jenkins
jenkins_admin_fullname: Jenkins Administrator

# Jenkins users
jenkins_users:
  - { username: "{{ jenkins_admin_username }}", password: "{{ jenkins_admin_password }}", fullname: "{{ jenkins_admin_fullname }}" }

# Jenkins plugins
jenkins_plugins:
  - blueocean
  - git
  - job-dsl
  - workflow-aggregator

# Jenkins jobs
jenkins_jobs: []
```

### DOCKER VARIABLES

```
---

# Jenkins Docker container settings
jenkins_container_name: jenkins-master
jenkins_container_image: jenkins
jenkins_container_host_port: "{{ jenkins_port }}"
jenkins_container_container_port: 8080
jenkins_container_data_volume: jenkins-data
```

## DEPENDENCIES

* [ansible-role-docker](https://github.com/erjac77/ansible-role-docker)

## EXAMPLE PLAYBOOK

```
---

- name: Install Jenkins CI on Docker
  hosts: localhost
  become: yes

  vars:
    jenkins_platform: Docker
    jenkins_admin_username: erjac
    jenkins_admin_password: erjac
    jenkins_admin_fullname: Eric Jacob
    jenkins_users:
      - { username: "{{ jenkins_admin_username }}", password: "{{ jenkins_admin_password }}", fullname: "{{ jenkins_admin_fullname }}" }
      - { username: jodoe, password: jodoe, fullname: John Doe }
      - { username: jadoe, password: jadoe, fullname: Jane Doe }
    jenkins_plugins:
      - blueocean
      - git
      - gogs-webhook
      - job-dsl
      - workflow-aggregator
    jenkins_jobs:
      - { name: my-awesome-job, config: "{{ lookup('file', 'files/jobs/test-job-config.xml') }}" }
      - { name: my-cool-job, config: "{{ lookup('file', 'files/jobs/test-job-config.xml') }}" }

  roles:
    - erjac77.jenkins
```

## LICENSE

Apache 2.0

## AUTHOR INFORMATION

Eric Jacob ([@erjac77](https://github.com/erjac77))