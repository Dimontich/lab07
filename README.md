# lab02
## Laboratory work II

Данная лабораторная работа посвещена изучению систем контроля версий на примере **Git**.

```bash
$ open https://git-scm.com
```

## Tasks

- [x] 1. Создать публичный репозиторий с названием **lab02** и с лиценцией **MIT**
- [x] 2. Сгенирировать токен для доступа к сервису **GitHub** с правами **repo**
- [x] 3. Ознакомиться со ссылками учебного материала
- [x] 4. Выполнить инструкцию учебного материала
- [x] 5. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Tutorial

```ShellSession
$ export GITHUB_USERNAME=Dimontich    # Установка переменной GITHUB_USERNAME
$ export GITHUB_EMAIL=dimasek28@gmail.com  # Установка переменной GITHUB_EMAIL
$ export GITHUB_TOKEN=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx # Установка переменной GITHUB_TOKEN
$ alias edit=nano      # Установка синонима команды edit
```

```ShellSession
$ cd ${GITHUB_USERNAME}/workspace    # Переход в папку workspace
$ source scripts/activate           # Выполнение скрипта 
```

```ShellSession
$ mkdir ~/.config         # Создание папки с конфигами
mkdir: cannot create directory ‘/home/dmitrij/.config’: File exists
$ cat > ~/.config/hub <<EOF      # Создание файла конфига hub и запись в него
github.com:
- user: ${GITHUB_USERNAME}
  oauth_token: ${GITHUB_TOKEN}
  protocol: https
EOF
$ git config --global hub.protocol https       # Установка переменной git
```

```ShellSession
$ mkdir projects/lab02 && cd projects/lab02   # Создание папки и переход в нее
$ git init    # Создание пустого репозиторий
Initialized empty Git repository in /home/dmitrij/Documents/Dimontich/workspace/projects/lab02/.git/
$ git config --global user.name ${GITHUB_USERNAME} # Установка переменной user.name 
$ git config --global user.email ${GITHUB_EMAIL} # Установка переменной user.email для
# check your git global settings.  
$ git config -e --global  # Вывод всего конфига из git
[user]
        email = dimasek28@gmail.com.com
        name = Dimontich
[hub]
        protocol = https


$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab02.git # Добавление ссылки на репозиторий на giyhub
$ git pull origin master # Перенос изменений
/* origin master
remote: Enumerating objects: 9, done.
remote: Counting objects: 100% (9/9), done.
remote: Compressing objects: 100% (8/8), done.
remote: Total 9 (delta 1), reused 0 (delta 0), pack-reused 0
Распаковка объектов: 100% (9/9), готово.
Из https://github.com/Dimontich/lab02
 * branch            master     -> FETCH_HEAD
 * [новая ветка]     master     -> origin/master */
                                

$ touch README.md.   # Создать README.md
$ git status. # Посмотреть статус репозитория
/*
На ветке master
Неотслеживаемые файлы:
  (используйте «git add <файл>…», чтобы добавить в то, что будет включено в коммит)

	README.md.
	node/
	scripts/

ничего не добавлено в коммит, но есть неотслеживаемые файлы (используйте «git add», чтобы отслеживать их)

*/



$ git add README.md.  # Добавить README.md в список фиксированных
$ git commit -m"added README.md" # Закоммитить фиксированные изменения
 
 t -m"added README.md"
На ветке master
Неотслеживаемые файлы:
	README.md.
	node/
	scripts/

ничего не добавлено в коммит, но есть неотслеживаемые файлы

 

$ git push origin master    # Отправить изменения на гитхаб
/*rigin master
Username for 'https://github.com': Dimontich
Password for 'https://Dimontich@github.com': 
Подсчет объектов: 3, готово.
Delta compression using up to 4 threads.
Сжатие объектов: 100% (2/2), готово.
Запись объектов: 100% (3/3), 304 bytes | 304.00 KiB/s, готово.
Total 3 (delta 0), reused 0 (delta 0)
To https://github.com/Dimontich/lab02.git
   d0718ca..db6f93f  master -> master
*/
```

Добавить на сервисе **GitHub** в репозитории **lab02** файл **.gitignore**
со следующем содержимом:

```ShellSession
*build*/
*install*/
*.swp
.idea/
```
Перенос изменений

```ShellSession
$ git pull origin master # Перенос изменений
/*rigin master
remote: Enumerating objects: 5, done.
remote: Counting objects: 100% (5/5), done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 3 (delta 1), reused 0 (delta 0), pack-reused 0
Распаковка объектов: 100% (3/3), готово.
Из https://github.com/Dimontich/lab02
 * branch            master     -> FETCH_HEAD
   db6f93f..3f930a2  master     -> origin/master
Обновление db6f93f..3f930a2
Fast-forward
 README.md | 15 ++++++++-------
 1 file changed, 8 insertions(+), 7 deletions(-)*/


$ git log.  # Просмотр коммитов
```

```ShellSession
$ mkdir sources.  # Создание папки sources
$ mkdir include   # Создание папки include
$ mkdir examples.   # Создание папки examples
$ cat > sources/print.cpp <<EOF. # Запись кода в файл
#include <print.hpp>

void print(const std::string& text, std::ostream& out)
{
  out << text;
}

void print(const std::string& text, std::ofstream& out)
{
  out << text;
}
EOF
```

```ShellSession
$ cat > include/print.hpp <<EOF
#include <fstream>
#include <iostream>
#include <string>

void print(const std::string& text, std::ofstream& out);
void print(const std::string& text, std::ostream& out = std::cout);
EOF
```

```ShellSession
$ cat > examples/example1.cpp <<EOF  # Запись кода в файл
#include <print.hpp>

int main(int argc, char** argv)
{
  print("hello");
}
EOF
```
Создание файла с кодом
```ShellSession
$ cat > examples/example2.cpp <<EOF # Запись кода в файл
#include <print.hpp>

#include <fstream>

int main(int argc, char** argv)
{
  std::ofstream file("log.txt");
  print(std::string("hello"), file);
}
EOF
```
Редактирование README.md

```ShellSession

$ edit README.mdm.   # Редактировать README.md
```

```ShellSession
$ git status # Просмотр статуса репозитория
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   README.md

Untracked files:
  (use "git add <file>..." to include in what will be committed)

        examples/
        include/
        sources/

no changes added to commit (use "git add" and/or "git commit -a")

$ git add  # Фиксация всех изменений
$ git commit -m"added sources"      # Коммит зафиксированных изменений
$ git push origin master   # Отправка изменений на гитхаб
```

## Report

```ShellSession
$ cd ~/workspace/labs/
$ export LAB_NUMBER=02
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER}.git tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gistup -m "lab${LAB_NUMBER}"
```

## Homework

### Part I

1. Создайте пустой репозиторий на сервисе github.com (или gitlab.com, или bitbucket.com).
2. Выполните инструкцию по созданию первого коммита на странице репозитория, созданного на предыдещем шаге.
3. Создайте файл `hello_world.cpp` в локальной копии репозитория (который должен был появиться на шаге 2). Реализуйте программу **Hello world** на языке C++ используя плохой стиль кода. Например, после заголовочных файлов вставьте строку `using namespace std;`.
4. Добавьте этот файл в локальную копию репозитория.
5. Закоммитьте изменения с *осмысленным* сообщением.
6. Изменитьте исходный код так, чтобы программа через стандартный поток ввода запрашивалось имя пользователя. А в стандартный поток вывода печаталось сообщение `Hello world from @name`, где `@name` имя пользователя.
7. Закоммитьте новую версию программы. Почему не надо добавлять файл повторно `git add`?
8. Запуште изменения в удалёный репозиторий.
9. Проверьте, что история коммитов доступна в удалёный репозитории.

### Part II

**Note:** *Работать продолжайте с теми же репоззиториями, что и в первой части задания.*
1. В локальной копии репозитория создайте локальную ветку `patch1`.
2. Внесите изменения в ветке `patch1` по исправлению кода и избавления от `using namespace std;`.
3. **commit**, **push** локальную ветку в удалённый репозиторий.
4. Проверьте, что ветка `patch1` доступна в удалёный репозитории.
5. Создайте pull-request `patch1 -> master`.
6. В локальной копии в ветке `patch1` добавьте в исходный код комментарии.
7. **commit**, **push**.
8. Проверьте, что новые изменения есть в созданном на **шаге 5** pull-request
9. В удалённый репозитории выполните  слияние PR `patch1 -> master` и удалите ветку `patch1` в удаленном репозитории.
10. Локально выполните **pull**.
11. С помощью команды **git log** просмотрите историю в локальной версии ветки `master`.
12. Удалите локальную ветку `patch1`.

### Part III

**Note:** *Работать продолжайте с теми же репоззиториями, что и в первой части задания.*
1. Создайте новую локальную ветку `patch2`.
2. Измените *code style* с помощью утилиты [**clang-format**](http://clang.llvm.org/docs/ClangFormat.html). Например, используя опцию `-style=Mozilla`.
3. **commit**, **push**, создайте pull-request `patch2 -> master`.
4. В ветке **master** в удаленном репозитории измените комментарии, например, расставьте знаки препинания, переведите комментарии на другой язык.
5. Убедитесь, что в pull-request появились *конфликтны*.
6. Для этого локально выполните **pull** + **rebase** (точную последовательность команд, следует узнать самостоятельно). **Исправьте конфликты**.
7. Сделайте *force push* в ветку `patch2`
8. Убедитель, что в pull-request пропали конфликтны. 
9. Вмержите pull-request `patch2 -> master`.

## Links

- [hub](https://hub.github.com/)
- [GitHub](https://github.com)
- [Bitbucket](https://bitbucket.org)
- [Gitlab](https://about.gitlab.com)
- [LearnGitBranching](http://learngitbranching.js.org/)

```
Copyright (c) 2015-2019 The ISC Authors
```
