[![Build Status](https://travis-ci.org/Dimontich/lab05.svg?branch=master)](https://travis-ci.org/Dimontich/lab05)

## Laboratory work IV

Данная лабораторная работа посвещена изучению систем непрерывной интеграции на примере сервиса **Travis CI**

```ShellSession
$ open https://travis-ci.org
```

## Tasks

- [x] 1. Авторизоваться на сервисе **Travis CI** с использованием **GitHub** аккаунта
- [x] 2. Создать публичный репозиторий с названием **lab05** на сервисе **GitHub**
- [x] 3. Ознакомиться со ссылками учебного материала
- [x] 4. Включить интеграцию сервиса **Travis CI** с созданным репозиторием
- [x] 5. Получить токен для **Travis CLI** с правами **repo** и **user**
- [x] 6. Получить фрагмент вставки значка сервиса **Travis CI** в формате **Markdown**
- [x] 7. Выполнить инструкцию учебного материала
- [x] 8. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Tutorial

Установка переменной

```ShellSession
$ export GITHUB_USERNAME=Dimontich   # Установка переменной GITHUB_USERNAME
$ export GITHUB_TOKEN=XXXXXXXXXXXXXXXXXXXXXXXX   # Установка переменной GITHUB_TOKEN
```

Подготовка

```ShellSession
$ cd ${GITHUB_USERNAME}/workspace   # Переход в директорию workspace
$ pushd .         # Сохранение директории 
/Dimontich/workspace /Dimontich/workspace
$ source scripts/activate       # Выполнение скрипта 
```

```ShellSession
$ \curl -sSL https://get.rvm.io | bash -s -- --ignore-dotfiles    # Установка rvm (ruby version manager)
Turning on ignore dotfiles mode.
Downloading https://github.com/rvm/rvm/archive/master.tar.gz
Creating group 'rvm'
Installing RVM to /usr/local/rvm/
Installation of RVM in /usr/local/rvm/ is almost complete:

  * First you need to add all users that will be using rvm to 'rvm' group,
    and logout - login again, anyone using rvm will be operating with `umask u=rwx,g=rwx,o=rx`.

  * To start using RVM you need to run `source /etc/profile.d/rvm.sh`
    in all your open shell windows, in rare cases you need to reopen all shell windows.
  * Please do NOT forget to add your users to the rvm group.
     The installer no longer auto-adds root or users to the rvm group. Admins must do this.
     Also, please note that group memberships are ONLY evaluated at login time.
     This means that users must log out then back in before group membership takes effect!
Thanks for installing RVM 🙏
Please consider donating to our open collective to help us maintain RVM.

👉  Donate: https://opencollective.com/rvm/donate
$ echo "source /etc/profile.d/rvm.sh" >> scripts/activate    # Добавление команды для запуска RVM в скрипт
$ . scripts/activate    # Выполнение скрипта (для запуска RVM)
$ rvm autolibs disable      # Не устанавливать зависимости
# Установить ruby 2.4.2
$ rvm install ruby-2.4.2 # (понадобилось также установить bzip2, zlib1g-dev)
ruby-2.4.2 - #removing src/ruby-2.4.2..
ruby-2.4.2 - #removing rubies/ruby-2.4.2..
Searching for binary rubies, this might take some time.
No binary rubies available for: debian/9/x86_64/ruby-2.4.2.
Continuing with compilation. Please read 'rvm help mount' to get more information on binary rubies.
Installing Ruby from source to: /usr/local/rvm/rubies/ruby-2.4.2, this may take a while depending on your cpu(s)...
ruby-2.4.2 - #downloading ruby-2.4.2, this may take a while depending on your connection...
ruby-2.4.2 - #extracting ruby-2.4.2 to /usr/local/rvm/src/ruby-2.4.2.....
ruby-2.4.2 - #configuring..................................................................
ruby-2.4.2 - #post-configuration..
ruby-2.4.2 - #compiling.............................................................................................-
ruby-2.4.2 - #installing...........
ruby-2.4.2 - #making binaries executable..
ruby-2.4.2 - #downloading rubygems-3.0.3
ruby-2.4.2 - #extracting rubygems-3.0.3......
ruby-2.4.2 - #removing old rubygems........
$LANG was empty, setting up LANG=C, if it fails again try setting LANG to something sane and try again.
ruby-2.4.2 - #installing rubygems-3.0.3...................................
ruby-2.4.2 - #gemset created /usr/local/rvm/gems/ruby-2.4.2@global
ruby-2.4.2 - #importing gemset /usr/local/rvm/gemsets/global.gemsthere was an error installing gem rubygems-bundler
.................................................................
ruby-2.4.2 - #generating global wrappers.......
ruby-2.4.2 - #gemset created /usr/local/rvm/gems/ruby-2.4.2
ruby-2.4.2 - #importing gemsetfile /usr/local/rvm/gemsets/default.gems evaluated to empty gem list
ruby-2.4.2 - #generating default wrappers.......
ruby-2.4.2 - #adjusting #shebangs for (gem irb erb ri rdoc testrb rake).
Install of ruby-2.4.2 - #complete 
Ruby was built without documentation, to build it run: rvm docs generate-ri
Making gemset ruby-2.4.2 pristine................................................................
Making gemset ruby-2.4.2@global pristine.................................................................
$ rvm use 2.4.2 --default   # Использовать ruby 2.4.2 по умолчанию
Using /usr/local/rvm/gems/ruby-2.4.2
$ gem install travis        # Установить пакет travis
Fetching multipart-post-2.1.1.gem
Fetching faraday-0.15.4.gem
Fetching faraday_middleware-0.13.1.gem
Fetching highline-1.7.10.gem
Fetching backports-3.15.0.gem
Fetching multi_json-1.13.1.gem
Fetching addressable-2.4.0.gem
Fetching net-http-persistent-2.9.4.gem
Fetching net-http-pipeline-1.0.1.gem
Fetching gh-0.15.1.gem
Fetching launchy-2.4.3.gem
Fetching ffi-1.11.1.gem
Fetching ethon-0.12.0.gem
Fetching typhoeus-0.8.0.gem
Fetching websocket-1.2.8.gem
Fetching pusher-client-0.6.2.gem
Fetching travis-1.8.10.gem
Successfully installed multipart-post-2.1.1
Successfully installed faraday-0.15.4
Successfully installed faraday_middleware-0.13.1
Successfully installed highline-1.7.10
Successfully installed backports-3.15.0
Successfully installed multi_json-1.13.1
Successfully installed addressable-2.4.0
Successfully installed net-http-persistent-2.9.4
Successfully installed net-http-pipeline-1.0.1
Successfully installed gh-0.15.1
Successfully installed launchy-2.4.3
Building native extensions. This could take a while...
Successfully installed ffi-1.11.1
Successfully installed ethon-0.12.0
Successfully installed typhoeus-0.8.0
Successfully installed websocket-1.2.8
Successfully installed pusher-client-0.6.2
Successfully installed travis-1.8.10
Parsing documentation for multipart-post-2.1.1
Installing ri documentation for multipart-post-2.1.1
Parsing documentation for faraday-0.15.4
Installing ri documentation for faraday-0.15.4
Parsing documentation for faraday_middleware-0.13.1
Installing ri documentation for faraday_middleware-0.13.1
Parsing documentation for highline-1.7.10
Installing ri documentation for highline-1.7.10
Parsing documentation for backports-3.15.0
Installing ri documentation for backports-3.15.0
Parsing documentation for multi_json-1.13.1
Installing ri documentation for multi_json-1.13.1
Parsing documentation for addressable-2.4.0
Installing ri documentation for addressable-2.4.0
Parsing documentation for net-http-persistent-2.9.4
Installing ri documentation for net-http-persistent-2.9.4
Parsing documentation for net-http-pipeline-1.0.1
Installing ri documentation for net-http-pipeline-1.0.1
Parsing documentation for gh-0.15.1
Installing ri documentation for gh-0.15.1
Parsing documentation for launchy-2.4.3
Installing ri documentation for launchy-2.4.3
Parsing documentation for ffi-1.11.1
Installing ri documentation for ffi-1.11.1
Parsing documentation for ethon-0.12.0
Installing ri documentation for ethon-0.12.0
Parsing documentation for typhoeus-0.8.0
Installing ri documentation for typhoeus-0.8.0
Parsing documentation for websocket-1.2.8
Installing ri documentation for websocket-1.2.8
Parsing documentation for pusher-client-0.6.2
Installing ri documentation for pusher-client-0.6.2
Parsing documentation for travis-1.8.10
Installing ri documentation for travis-1.8.10
Done installing documentation for multipart-post, faraday, faraday_middleware, highline, backports, multi_json, addressable, net-http-persistent, net-http-pipeline, gh, launchy, ffi, ethon, typhoeus, websocket, pusher-client, travis after 19 seconds
17 gems installed
```

Получить необходимые файлы

```ShellSession
$ git clone https://github.com/${GITHUB_USERNAME}/lab03 projects/lab05  # Клонирование репо
$ cd projects/lab05   # Переход в директорию
$ git remote remove origin    # Удаление ссылки на репозиторий
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab05  # Добавление ссылки на репозиторий
```

Добавить в конфиг travis информацию о языке (отвечает за дополнительные пакеты, которые будут установлены (например, g++ gcc))

```ShellSession
$ cat > .travis.yml <<EOF
language: cpp
EOF
```

Добавить в конфиг travis команды, которые будут выполнены после загрузки и установки 

```ShellSession
$ cat >> .travis.yml <<EOF

script:
- cmake -H. -B_build -DCMAKE_INSTALL_PREFIX=_install
- cmake --build _build
- cmake --build _build --target install
EOF
```

Добавить в конфиг travis информацию об установке пакетов

```ShellSession
$ cat >> .travis.yml <<EOF

addons:
  apt:
    sources:
      - george-edison55-precise-backports
    packages:
      - cmake
      - cmake-data
EOF
```

Авторизация в travis

```ShellSession
$ travis login --github-token ${GITHUB_TOKEN}
Successfully logged in as Dimontich!
```

Статический анализ конфигурационного файла travis

```ShellSession
$ travis lint
Warnings for .travis.yml:
[x] value for addons section is empty, dropping
[x] in addons section: unexpected key apt, dropping
```

Добавить строку с рисунком статуса сборка travis в начало README.md

```ShellSession
$ sed -i '1i [![Build Status](https://travis-ci.org/Dimontich/lab03.svg?branch=master)](https://travis-ci.org/Dimontich/lab03)' README.md
```

Отправка изменений

```ShellSession
$ git add .travis.yml #  Фиксируем
$ git add README.md   # Фиксируем
$ git commit -m"added CI"   # Коммитим
[master f408d4c] added CI
 2 files changed, 15 insertions(+)
 create mode 100644 .travis.yml
$ git push origin master    # Отправляем в удаленный репо
Counting objects: 48, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (44/44), done.
Writing objects: 100% (48/48), 18.60 KiB | 0 bytes/s, done.
Total 48 (delta 12), reused 0 (delta 0)
remote: Resolving deltas: 100% (12/12), done.
To https://github.com/Dimontich/lab05
 * [new branch]      master -> master
```

Проверка работоспособности travis

```ShellSession
$ travis lint       # Статический анализ конфигурационного файла
Warnings for .travis.yml:
[x] value for addons section is empty, dropping
[x] in addons section: unexpected key apt, dropping
$ travis accounts       # Аккаунты, привязанные к travis
Dimontich (Dimontich): subscribed, 10 repositories
$ travis sync           # Синхронизация с сервисом
synchronizing: . done
$ travis repos          # Просмотр состояний репозиториев
Dimontich/-II (active: no, admin: yes, push: yes, pull: yes)
Description: ???

Dimontich/RK02_TiMP (active: no, admin: yes, push: yes, pull: yes)
Description: Рубежный контроль по ТиМП

Dimontich/dz-2 (active: no, admin: yes, push: yes, pull: yes)
Description: ???

Dimontich/dz-2-final (active: no, admin: yes, push: yes, pull: yes)
Description: ???

Dimontich/lab00 (active: no, admin: yes, push: yes, pull: yes)
Description: Изучение систем обмена данными

Dimontich/lab01 (active: no, admin: yes, push: yes, pull: yes)
Description: Изучение утилит для разработки проектов

Dimontich/lab02 (active: no, admin: yes, push: yes, pull: yes)
Description: ???

Dimontich/lab03 (active: no, admin: yes, push: yes, pull: yes)
Description: ???

Dimontich/lab05 (active: yes, admin: yes, push: yes, pull: yes)
Description: ???

Dimontich/laba (active: no, admin: yes, push: yes, pull: yes)
Description: ???
$ travis enable         # Включение CI для текущего репо
Dimontich/lab05: enabled :)
$ travis whatsup        # Статус
Dimontich/lab05 passed: #1
$ travis branches       # Ветки
master:  #1    passed     added CI
$ travis history        # История сборок
#1 passed:       master added CI
$ travis show           # Подробная информация
Job #1.1:  added CI
State:         passed
Type:          push
Branch:        master
Compare URL:   https://github.com/Dimontich/lab05/compare/608802cba8f4^...f408d4c36e90
Duration:      27 sec
Started:       2019-06-10 08:29:28
Finished:      2019-06-10 08:29:55
Allow Failure: false
Config:        os: linux
```

## Report

```ShellSession
$ popd
$ export LAB_NUMBER=04
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gistup -m "lab${LAB_NUMBER}"
```

## Homework

Вы продолжаете проходить стажировку в "Formatter Inc." (см [подробности](https://github.com/tp-labs/lab03#Homework)).

В прошлый раз ваше задание заключалось в настройке автоматизированной системы **CMake**.

Сейчас вам требуется настроить систему непрерывной интеграции для библиотек и приложений, с которыми вы работали в [прошлый раз](https://github.com/tp-labs/lab03#Homework). Настройте сборочные процедуры на различных платформах:
* используйте [TravisCI](https://travis-ci.com/) для сборки на операционной системе **Linux** с использованием компиляторов **gcc** и **clang**;
* используйте [AppVeyor](https://www.appveyor.com/) для сборки на операционной системе **Windows**.

## Links

- [Travis Client](https://github.com/travis-ci/travis.rb)
- [AppVeyour](https://www.appveyor.com/)
- [GitLab CI](https://about.gitlab.com/gitlab-ci/)

```
Copyright (c) 2015-2019 The ISC Authors
```
