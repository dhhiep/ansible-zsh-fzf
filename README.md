### Environment
  ```
  + Ubuntu 16.04
  + Ansible 2.5.2
  + Ansible Galaxy 2.5.2
  + Vagrant 2.1.1
  + Virtualbox 5.2.1.12
  ```

#### Setup Step by Step
  1. Install python, pip and ansible
  ```bash
  sudo apt-get install -y python2.7 python-pip
  ```

  Then anything we intall with pip will be inside that virtual environment
  ```bash
  pip install ansible
  ```

  2. Add pre-tasks to preparing base package to install another packages
  ```bash
  mkdir roles
  ansible-galaxy init roles/pre_tasks
  ```

  Add code below to roles/pre-tasks/tasks/main.yml
  ```yaml
  ---
  - include: update_system.yml
  - include: packages.yml
  ```

  Add code below to roles/pre-tasks/tasks/update_system.yml
  ```yaml
  ---
  - name: apt-get update
    raw: apt-get update -qq
  - name: install python 2.7 and aptitude
    raw: apt-get install -qq python2.7 aptitude
  - name: set python 2.7 as default
    raw: update-alternatives --install /usr/bin/python python /usr/bin/python2.7 1
  ```

  Add code below to roles/pre-tasks/tasks/packages.yml
  ```yaml
  ---
  - name: Install packages
    package:
      name: '{{item}}'
      state: present
    with_items: ['git-core', 'htop']

  ```

  3. Install and config ZSH
  ```bash
  ansible-galaxy init roles/zsh
  ```

  Add code below to roles/zsh/tasks/main.yml
  ```yaml
  ---
  - name: Install ZSH
    package:
      name: zsh
      state: present
  ```

