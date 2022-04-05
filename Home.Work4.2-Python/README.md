# Домашнее задание к занятию "4.2. Использование Python для решения типовых DevOps задач"
## Обязательные задания


1. Есть скрипт:
```
#!/usr/bin/env python3
a = 1
b = '2'
c = a + b
```

    Какое значение будет присвоено переменной c?
#### Ответ:
    
user@Daemon:~/MY-APP-VSCODE$ /bin/python3 /home/user/MY-APP-VSCODE/1home.py
Traceback (most recent call last):

File "/home/user/MY-APP-VSCODE/1home.py", line 4, in <module>
  
c = a + b
  
TypeError: unsupported operand type(s) for +: 'int' and 'str' - ошибка операции сложения, т.к. складываются разные типы данных: число и строка.
    
    Как получить для переменной c значение 12?
#### Ответ:    
```  
#!/usr/bin/env python3
a = '1'
b = '2'
c = a + b
print (c)
```
user@Daemon:~/MY-APP-VSCODE$ /bin/python3 /home/user/MY-APP-VSCODE/1home.py
12

    Как получить для переменной c значение 3?
#### Ответ:    
```  
#!/usr/bin/env python3
a = 1
b = 2
c = a + b
print (c)
```
user@Daemon:~/MY-APP-VSCODE$ /bin/python3 /home/user/MY-APP-VSCODE/1home.py
3

2. Мы устроились на работу в компанию, где раньше уже был DevOps Engineer. Он написал скрипт, позволяющий узнать, какие файлы модифицированы в репозитории, относительно локальных изменений. Этим скриптом недовольно начальство, потому что в его выводе есть не все изменённые файлы, а также непонятен полный путь к директории, где они находятся. Как можно доработать скрипт ниже, чтобы он исполнял требования вашего руководителя?
```
#!/usr/bin/env python3

import os

bash_command = ["cd ~/netology/sysadm-homeworks", "git status"]
result_os = os.popen(' && '.join(bash_command)).read()
is_change = False
for result in result_os.split('\n'):
    if result.find('modified') != -1:
        prepare_result = result.replace('\tmodified:   ', '')
        print(prepare_result)
        break
```
#### Ответ:    
