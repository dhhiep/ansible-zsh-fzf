#### Setup Step by Step
  1. Install python, pip and ansible
  ```bash
  sudo apt-get install -y python2.7 python-pip
  ```

  # Then anything we intall with pip will be inside that virtual environment
  ```bash
  pip install ansible
  ```

  2. Add pre-tasks to preparing base package to install another packages
  ```bash
  mkdir roles
  cd roles
  ansible-galaxy init roles/pre_tasks
  ```

  Add below code to roles/pre-tasks/tasks/main.yml
  ```yaml
  ---
  - name: apt-get update
    raw: apt-get update -qq
  - name: install python 2.7 and aptitude
    raw: apt-get install -qq python2.7 aptitude
  - name: set python 2.7 as default
    raw: update-alternatives --install /usr/bin/python python /usr/bin/python2.7 1
  ```

