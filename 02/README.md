# Домашнее задание 2
## Shell
### Задание
Нужно написать упрощенную версию командной строки. Она должна
принимать на вход строки вида:
```console
> command_name param1 param2 ...
```
и выполнять их при помощи вызова команды command_name с
параметрами. То есть работать как терминал. При этом нужно, чтобы
программа правильно обрабатывала строки, заключенные в кавычки,
как одну большую строку, даже если там есть пробелы. Комментарии
тоже должны обрабатываться верно, то есть обрезаться.

Кроме того, должен поддерживаться конвейер, или иначе говоря pipe,
который делается символом |, а так же перенаправление вывода в
файл (>, >>).

Длина входной строки не ограничена, сохранять ее в буфер заранее
предаллоцированного размера нельзя.

На выход программа должна печатать все то же, что и обычный
терминал.

Запрещается использовать функции вроде system(), popen(), или еще
каким-то образом пытаться обращаться к терминалу или
автоматическому созданию конвейеров.

Необходимо использовать функции pipe(), dup/dup2(), fork(), wait,
open, close, как минимум одну из функций семейства exec: execl,
execle, execlp, execv, execvp, execvP.

#### Примеры ввода:

- Распечатать список процессов и найти среди них строку init.
```console
    > ps aux | grep init
```
- Выполнить код в python и выполнить поиск по его результату:
```console
    > echo "print('result is ', 123 + 456)" | python -i | grep result
```
- Печать экранированной кавычки в файл и его вывод:
```console
    > echo "some text\" with quote" > test.txt
    > cat test.txt
```
- Дописывание в файл:
```console
    > echo "first data" > test.txt
    > echo "second" >> test.txt
    > cat test.txt
```
- Запустить интерактивную консоль python и что-то в ней сделать:
```console
    > python
    >>> print(1 + 2)
    >>> 3
```
#### Разбалловка:

  - 15 баллов: все описанное выше,
  - 20 баллов: поддержка операторов && и ||,
  - 25 баллов: то же, что на 20 + поддержка &.

#### Вход:
команды и их аргументы, операторы перенаправления
выводов/вводов.

#### Выход:
то же, что выводит терминал.

#### С чего начать? 
Рекомендуемый план выполнения задания такой:

- реализовать парсер команд. Это достаточно самостоятельная часть решения, и в
  то же время самая простая в плане понимания что делать. Стоит сделать ее в
  отдельном файле, и отдельно протестировать. Ваш парсер должен превращать
  введенную команду вида "command arg1 arg2 ... argN" в отдельно "command" и
  некий массив ее аргументов, например, вида const char **args, int arg_count.
  Парсер должен особым образом обрабатывать |, считая его разделителем команд.
  Наример, пусть одну команду парсер разбирает в
```c
      struct cmd {
          const char *name;
          const char **argv;
          int argc;
      }
```
  Тогда парсер должен возвращать массив struct cmd. Если нет |, то в нем будет
  1 элемент;

- реализовать выполнение команд без |, >, >>. Просто чтобы выполнялась одна
  команда;

- добавить |, >, >>.

Архитектура решения может быть следующей: есть процесс-терминал, который
читает команды пользователя. На каждую команду он делает fork(). Появившийся
потомок запускает команду используя exec* функции. Родительский процесс ждет
завершения ребенка. Для | терминал создает pipe, которым связывает вывод одного
потомка с вводом другого. Для > и >> терминал открывает файл и при помощи
dup/dup2 подменяет им стандартный поток вывода потомка.

### Коментарии по решению
Немного поправил Makefile, чтобы он работал с моей структурой каталогов, реализованы команды но 20 баллов. Для запуска тестов на 20 баллов - ```make test max=20```, на 15 соответственно - ```make test max=15```.
