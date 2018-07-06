### Environment
  ```
  + Ubuntu 16.04
  + Ansible 2.5.2
  + Ansible Galaxy 2.5.2
  + Vagrant 2.1.1
  + Virtualbox 5.2.1.12
  ```

#### Setup Step by Step
  ##### I. Install python, pip and ansible
  1. Install python
  ```bash
  sudo apt-get install -y python2.7 python-pip
  ```

  2. Install Ansible
  ```bash
  pip install ansible
  ```

  ##### II. Update global variables
  1. Add file vars/main.yml
  ```yaml
  ---
  remove_oh_my_zsh_if_existed: false
  default_shell: zsh
  debug_mode: true
  main_user: vagrant
  main_dir: "/home/{{main_user}}"
  ```

  2. Add config to playbook.yml
  ```yaml
  vars_files:
    - vars/main.yml
  ```

  ##### III. Add pre-tasks to preparing base package to install another packages
  1. Preparing structure by ansible galaxy for pre-tasks
  ```bash
  mkdir roles
  ansible-galaxy init roles/pre_tasks
  ```

  2. Add code below to roles/pre-tasks/tasks/main.yml
  ```yaml
  ---
  - include: update_system.yml
  - include: packages.yml
  ```

  3. Add code below to roles/pre-tasks/tasks/update_system.yml
  ```yaml
  ---
  - name: apt-get update
    raw: apt-get update -qq
  - name: install python 2.7 and aptitude
    raw: apt-get install -qq python2.7 aptitude
  - name: set python 2.7 as default
    raw: update-alternatives --install /usr/bin/python python /usr/bin/python2.7 1
  ```

  4. Add code below to roles/pre-tasks/tasks/packages.yml
  ```yaml
  ---
  - name: Install packages
    package:
      name: '{{item}}'
      state: present
    with_items: ['git-core', 'htop', 'curl']
  ```

  5. Add roles pre-tasks to playbook.yml
  ```
  roles:
    - pre-tasks
  ```

  ##### III. Install and config ZSH
  1. Preparing structure by ansible galaxy for zsh
  ```bash
  ansible-galaxy init roles/zsh
  ```

  2. Add code below to roles/zsh/tasks/main.yml
  ```yaml
  ---
  - name: Install ZSH
    package:
      name: zsh
      state: present
  ```

  3. Add roles zsh to playbook.yml
  ```
  roles:
    - pre-tasks
    - zsh
  ```

  ##### IV. Install Oh-my-ZSH
  1. Preparing structure by ansible galaxy for Oh My ZSH
  ```bash
  ansible-galaxy init roles/oh_my_zsh
  ```

  2. Add code below to roles/oh_my_zsh/tasks/main.yml
  ```yaml
  ---
  - name: Load install script from server
    get_url:
      url: https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh
      dest: "{{main_dir}}/oh-my-zsh.sh"
      mode: 0777

  - name: Run script as {{main_user}}
    become: true
    become_user: "{{main_user}}"
    command: "sh -C {{main_dir}}/oh-my-zsh.sh"

  - name: Change user ({{main_user}}) shell to {{default_shell}}
    become: true
    user:
      name: main_user
      shell: /bin/{{default_shell}}
  ```

  3. Add roles oh_my_zsh to playbook.yml
  ```
  roles:
    - pre-tasks
    - zsh
    - oh_my_zsh
  ```
