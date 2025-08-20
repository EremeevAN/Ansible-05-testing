## Molecule
Выполнена команда molecule init scenario --driver-name docker
Результат:
![image](https://github.com/EremeevAN/Ansible-05-testing/blob/main/images/1.png)
Правим файл molecule.yaml
```yaml
---
driver:
  name: docker
lint: |
  ansible-lint .
  yamllint .

platforms:
  - name: ubuntu
    image: docker.io/ubuntu:latest
    pre_build_image: true
  - name: centos
    image: docker.io/oraclelinux:8
    pre_build_image: true
```
Проводим тест командой molecule test и получаем первые ошибки:
![image](https://github.com/EremeevAN/Ansible-05-testing/blob/main/images/2.png)
Добавляем d файл meta/main.yaml
 ```yaml
  role_name: vector
  namespace: vector
```
Запустили снова и на выходе получили результат:
![image](https://github.com/EremeevAN/Ansible-05-testing/blob/main/images/3.png)
Добавили файл verify.yml
 ```yaml
---
- name: Verify
  hosts: all
  gather_facts: false

  tasks:
    - name: Get Vector version
      ansible.builtin.command: vector --version
      changed_when: false
      register: vector_version
      failed_when: vector_version.rc != 0

    - name: Vector version ouput
      ansible.builtin.debug:
        var: vector_version.stdout

    - name: Validation Vector configuration
      ansible.builtin.command: vector validate --no-environment --config-yaml /etc/vector/vector.yaml
      changed_when: false
      register: vector_validate
      failed_when: vector_validate.rc != 0

    - name: Vector configuration ouput
      ansible.builtin.debug:
        var: vector_validate.stdout
```
Запускаем тестирование и вот что на выходе
![image](https://github.com/EremeevAN/Ansible-05-testing/blob/main/images/4.png)
Ссылка на репозиторий: https://github.com/EremeevAN/vector-role/releases/tag/v1.0.1
## Tox
Выполняем команды docker run --privileged=True -v <path_to_repo>:/opt/vector-role -w /opt/vector-role -it aragast/netology:latest /bin/bash и tox. Получаем результат:
![image](https://github.com/EremeevAN/Ansible-05-testing/blob/main/images/5.png)
P.S. использовал команду tox -e py39-ansible{212,30} и получил такой результат:
![image](https://github.com/EremeevAN/Ansible-05-testing/blob/main/images/6.png)
Ссылка на репозиторий: https://github.com/EremeevAN/vector-role/releases/tag/v1.0.2