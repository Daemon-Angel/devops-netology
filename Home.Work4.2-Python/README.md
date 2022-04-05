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
    
user@Daemon:~/MY-APP-VSCODE$ /bin/python3 /home/user/MY-APP-VSCODE/1home.py
Traceback (most recent call last):

File "/home/user/MY-APP-VSCODE/1home.py", line 4, in <module>
  
c = a + b
  
TypeError: unsupported operand type(s) for +: 'int' and 'str' - ошибка операции сложения, т.к. складываются разные типы данных: число и строка.
    
    Как получить для переменной c значение 12?
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
```  
#!/usr/bin/env python3
a = 1
b = 2
c = a + b
print (c)
```
user@Daemon:~/MY-APP-VSCODE$ /bin/python3 /home/user/MY-APP-VSCODE/1home.py
3


