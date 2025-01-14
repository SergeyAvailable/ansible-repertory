Vault file или string? каким образом лучше использовать ansible-vault



[![Build Status](https://travis-ci.org/express42/ansible-repertory.svg?branch=master)](https://travis-ci.org/express42/ansible-repertory)

# Описание
Хороший шаблон для организации Ansible проекта. Описывает необходимую структуру для создания хорошо читаемого проекта.

## Базовые принципы
* Один playbook - одно приложение
* Все переменные должны определятся в файлах для переменных окружения(environmets) 

# Структура директорий

* `environments/` - корневая директория для окружений (prod,dev,stage..)
  * `environment_dir/` - директория содержащая переменные и хосты для каждого окружения
    * `group_vars/` - переменные определяемые для группы
    * `host_vars/` - переменные определяемые для хостов
    * `play_vars/` - переменные определямые на каждый play в playbooks
    * `inventory_file` - список хостов для окружения
* `molecule/` - конфигурация molecule v2
  * `resources/` - shared among molecule scenarios resources
  * `scenario_name/` - specific molecule scenarios divided by platform, default - docker containers
* `playbooks/` - директория для playbooks, один playbook - одно приложение
* `roles/` - кастомные роли
* `ansible.cfg` - конфиг файл проекта Ansible
* `requirements.txt` - python requirements
* `requirements.yml` - файл зависимстей ролей и коллекций
* `site.yml` - playbook содержащий в себе все приложения проекта

# Где что расположить

## Переменные
* Все переменные должны быть определены в соответсвующем окружении(environmetns)
* Приоритет переменных http://docs.ansible.com/ansible/playbooks_variables.html#variable-precedence-where-should-i-put-a-variable
* старайтесь использовать только следующие расположения переменных:
  * Environment host_vars
  * Environment group_vars
  * Environment play_vars
* Избегайте объявления переменных в playbook

## Tasks
* Все tasks должны быть определены в ролях

## Playbooks
* Playbooks содержат только роли, группы хостов и файлы переменных
* Playbook должен содержать все нужное для запуска приложения
* Избегайте описания переменных и tasls в playbooks
* You can use `site.yml` as the only entrypoint for your configuration

# Getting started

## Fast start
* Do not forget use Python 2.7 instead of 3.x. Molecule and Ansible doesn't support Python 3.x
* Clone git repo
```sh
git clone git@github.com:express42/ansible-repertory.git
``` 
* Install [the latest release of Vagrant][Vagrant] if you will use ansible-repertory with Parallels VMs
* Then install Vagrant plugin for Parallels
```sh
vagrant plugin install vagrant-parallels
```
* Install prerequisites
```sh
cd ansible-repertory
ansible-galaxy install -r requirements.yml
pip install -r requirements.txt
touch vault.key
```
* Run tests
```sh
molecule test
```

## Using with your infrastructure
* Remove excessive roles from requirements.yml, python modules from requirements.txt and playbooks
* Make changes to site.yml
* Make changes to vars files in `example` environment
* Test changes with command
```sh
molecule test
```
* Copy molecule environment to new environment
* Make changes to inventory and vars files
* Run command
```sh
ansible-playbook site.yml --inventory-file=./environments/new_environment/inventory
```
## Molecule

```sh
molecule list
molecule converge
molecule status
molecule login common
molecule destroy
```

# Testing using Travis-CI
* Fork git repository
* Add repository into Travis-CI
* Push new commits

# ToDo
* Update default packages playbook
* Add more playbooks or list recommended ones
* Add base role (as it is in testo)

# Known issues
* 'zabbix_url' variable used in playbooks dj-wasabi.zabbix-agent and dj-wasabi.zabbix-server for different purposes
  * Workaround: redefine 'zabbix_url' in play_vars
* API calls in playbook dj-wasabi.zabbix-agent made from several servers can interfere with each other
  * Workaround: make API calls serial
* While using VirtualBox for testing you should change all mentions of interface 'eth0' to 'eth1'

# Links
* [Ansible documentation](https://docs.ansible.com/ansible/index.html "Ansible documentation")
* [Molecule documentation](https://molecule.readthedocs.io/en/master/ "Molecule documentation")
* [Vagrant documentation](https://www.vagrantup.com/docs/ "Vagrant documentation")

[Vagrant]: https://www.vagrantup.com/downloads.html "Vagrant"
