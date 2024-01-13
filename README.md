# ansible-role-jupyterhub-install

#### Краткое описание по ansible-роли 
Роль в себе содержит перменные для установки пакетов и перменные для ansible роли, путь roles/jupyter_install/defaults/main.yaml
```main.yaml
jupyter_apt_packages:
  - python3 
  - python3-dev 
  - git 
  - curl
jupyter_admin_user: admin
ansible_user: user
jupyter_admin_token: true
```
#### Хэндлеры для перезапуска служб для приложения, путь roles/jupyter_install/handlers
```yaml
---
- name: restart delete-sessions.service
  become: true
  systemd:
    daemon_reload: true
    name: delete-sessions.service
    state: restarted

- name: restart delete-users.service
  become: true
  systemd:
    daemon_reload: true
    name: delete-users.service
    state: restarted

- name: restart jupyterhub.service
  become: true
  systemd:
    daemon_reload: true
    name: jupyterhub.service
    state: restarted
```

#### Три таски для установки, пост-установки, и удаления приложения
install.yaml
launch_services.yaml
uninstall.yaml

#### Шаблоны(templates)
Шаблоны используются для генерации конфигурационных файлов или других текстовых файлов на основе переменных и условий. Основное предназначение шаблонов в Ansible - это динамическое создание файлов с учетом различных параметров, вместо использования статических файлов.

#### Файл hosts 
Файл hosts в Ansible используется для определения инвентаря, т.е., списка хостов (серверов или устройств), на которых Ansible будет выполнять свои задачи. Этот файл содержит информацию о доступе к хостам, переменные, ассоциированные с ними, и другие параметры, которые Ansible использует для выполнения задач.

#### Файл install_jupyterhub.yaml он же файл ansible-playbook
Файл плейбука в Ansible используется для определения набора задач (tasks), которые нужно выполнить на целевых хостах. Когда речь идет о запуске Ansible роли, файл плейбука может использоваться для вызова и использования роли в контексте конкретного плейбука или сценария.

## Как пользоваться
1. Склонируйте репозиторий
2. На своем пк/или сервере выполните установку ansible и python
3. Выставите переменные roles/jupyter_install/defaults/main.yaml если потребуется
4. Выставите ip-адрес целевого хоста на который будет производиться установка приложения jupyterhub в файле hosts
```hosts
[APP]
192.168.1.20
```
5. Выполните команду для установки приложения jupyterhub на linux сервер:
```bash
ansible-playbook install_jupyterhub.yaml --private-key "<path_to_private_key>" -u <remote_username> -i hosts -e "ansible_port=<remote_port>" -e -e "ansible_sudo_pass=<remote_user_sudo_password>"  --tags install
```
6. Выполните команду для для пост установки:
Данную команду следует выполнять когда будет сгенирован токен для администратора приложения jupyterhub на странице http://ip_address/hub/token
``` bash
ansible-playbook install_jupyterhub.yaml --private-key "<path_to_private_key>" -u <remote_username> -i hosts -e "ansible_port=<remote_port>" -e -e "ansible_sudo_pass=<remote_user_sudo_password>" -e "jupyter_admin_token=<jupyter_admin_token>"  --tags launch_services
```
7. Если требуется удаление приложения jupyterhub с сервера, то выполните команду:
```
ansible-playbook install_jupyterhub.yaml --private-key "<path_to_private_key>" -u <remote_username> -i hosts -e "ansible_port=<remote_port>" -e -e "ansible_sudo_pass=<remote_user_sudo_password>"  --tags install
```
Пример для установки:
```bash
 ansible-playbook -v install_jupyterhub.yaml --private-key "/Users/john/.ssh/id_ed25519" -u user -i hosts -e "ansible_port=22" -e "ansible_sudo_pass=1"  --tags install
 ```

Пример для пост установки:
```bash
ansible-playbook -v install_jupyterhub.yaml --private-key "/Users/john/.ssh/id_ed25519" -u user -i hosts -e "ansible_port=22" -e "ansible_sudo_pass=1" --extra-vars "jupyter_admin_token=1492c17a76044d15b8873df2485e9698"  --tags launch_services
 ```

8. Пример для удаления приложения:

```bash
ansible-playbook -v install_jupyterhub.yaml --private-key "/Users/john/.ssh/id_ed25519" -u user -i hosts -e "ansible_port=22" -e "ansible_sudo_pass=1"  --tags uninstall
 ```

По итогу после установки можно выполнить авторизацию на сервисе и сразу перейти в проект темплейт и выполнять лабараторные работы. 

