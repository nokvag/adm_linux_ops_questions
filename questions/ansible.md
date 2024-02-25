## Ansible


<details>
  <summary>Чем отличаются Ansible модули *raw*, *command* и *shell*?</summary>

Модуль *raw* отличается от *command* и *shell* тем, что не выполняет дополнительную обработку выполнения команды. Эти дополнительные обработки присутствуют в почти любом модуле Ansible. Модуль *raw* передает команду, как есть, в "сыром" (raw) виде без проверок.
Модули *command* и *shell* отличаются тем, что в модуле *command* команда выполняется без прохождения через командную оболочку `/bin/sh`. Поэтому переменные определенные в оболочке и перенаправления - конвееры работать не будут. Модуль *shell* выполняет команды через оболочку по умолчанию `/bin/sh`. Поэтому там будут доступны переменные оболочки и перенаправления.

</details>



<details>
  <summary>На всех серверах должен быть набор пользователей, с доступом по ssh-ключу, стандартный модуль user не позволяет вносить ssh ключ в authorized_keys. Предложите решение.</summary>

1. Использовать модуль `authorized_key` для добавления ключей.
2. Использовать модуль `shell`, чтобы вручную с использованием команды `cat {{ PUBLIC_SSH_KEY }} >> /home/{{ USER }}/.ssh/authorized_keys` добавить ключ. В данном случае шаблоны Jinja2 PUBLIC_SSH_KEY и USER должны быть заданы.

</details>



<details>
  <summary>Есть группы пользователей, которые должны заводиться не на всех серверах. Как ограничить заведение пользователей?</summary>

Сгруппировать сервера, на которых должны заводиться группы пользователей, в инвентори или написать в плейбуке условие, которому передаётся список серверов, на которых необходимо выполнить задачу.

</details>



<details>
  <summary>На новом сервере не установлен Python, который требуется для работы Ansible. Как выполнить установку Python на сервере используя Ansible?</summary>

Использовать модуль `raw`, которому необходимо передать команду для установки python на сервере. Модуль `raw` принимает команду без дополнительной обработки Python и выполняет её на сервере.

</details>



<details>
  <summary>Что такое роль в Ansible? Что содержит в себе Ansible роль?</summary>

Ansible роль представляет собой структурированный плейбук, содержащий, как минимум, набор задач (tasks) и дополнительно - обработчики событий (handlers), переменных (default и vars), файлов (files), шаблонов (templates), описание и зависимости (metadata) и тесты (tests).

</details>



<details>
  <summary>В Ansible роли есть директории *vars* и *default*. Что они содержат и чем отличаются?</summary>

Ansible применяет порядок приоритета переменных. Ниже представлен список в порядке повышения приоритета.

1. command line values (for example, -u my_user, these are not variables)
2. role defaults (defined in role/defaults/main.yml)
3. inventory file or script group vars
4. inventory group_vars/all
5. playbook group_vars/all
6. inventory group_vars/*
7. playbook group_vars/*
8. inventory file or script host vars
9. inventory host_vars/*
10. playbook host_vars/*
11. host facts / cached set_facts
12. play vars
13. play vars_prompt
14. play vars_files
15. role vars (определяемые в role/vars/main.yml)
16. block vars (только для задач в `block`)
17. task vars (только для задач)
18. include_vars
19. set_facts / registered vars
20. role (и include_role) params
21. include params
22. extra vars (например, -e "user=my_user")(всегда приоритетнее)

Соответственно переменные в *vars* будут приорететнее, чем в *defaults*.

</details>



<details>
  <summary>В Ansible роли есть директории *file* и *templates*. Что они содержат и чем отличаются?</summary>

*files* - содержит файлы, которые будут скопированы на настраиваемые хосты; так же — может содержать скрипты, которые позже будут запускаться на хостах.

*templates* - содержит шаблоны файлов с переменными.

</details>



<details>
  <summary>По-умолчанию, в Ansible все задачи из списка выполняются параллельно на всех хостах, которые указаны в `hosts`. Как сделать так, чтобы задачи выполнялись последовательно по хостам?</summary>

Необходимо установить параметр `serial: 1`, чтобы определить количество хостов, на которых будут выполняться паралелльно задачи. Значение 1 будет значить, что все задачи будут проходить параллельно по 1 хосту за раз.

Ссылка на документацию: https://docs.ansible.com/ansible/latest/user_guide/playbooks_strategies.html#setting-the-batch-size-with-serial

</details>
