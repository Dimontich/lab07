[![Build Status](https://travis-ci.org/Dimontich/lab06.svg?branch=master)](https://travis-ci.org/Dimontich/lab06)

## Laboratory work VI

Данная лабораторная работа посвещена изучению средств пакетирования на примере **CPack**

```ShellSession
$ open https://cmake.org/Wiki/CMake:CPackPackageGenerators
```

## Tasks

- [x] 1. Создать публичный репозиторий с названием **lab06** на сервисе **GitHub**
- [x] 2. Выполнить инструкцию учебного материала
- [x] 3. Ознакомиться со ссылками учебного материала
- [x] 4. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Tutorial

Установка переменных

```ShellSession
$ export GITHUB_USERNAME=Dimontich   # Установка переменной GITHUB_USERNAME
$ export GITHUB_EMAIL=dimasek28@gmail.com   # Установка переменной GITHUB_EMAIL
$ alias edit=nano   # Установка команды edit
$ alias gsed=sed # for *-nix system   # Установка команды gsed
```

Подготовка

```ShellSession
$ cd ${GITHUB_USERNAME}/workspace   # Переход в директорию workspace
$ pushd .   # Сохранение директории
$ source scripts/activate    # Выполнение скрипта
```

Получаем файлы прошлой лабораторной

```ShellSession
$ git clone https://github.com/${GITHUB_USERNAME}/lab05 projects/lab06  # Клонирование репо
Cloning into 'projects/lab06'...
remote: Enumerating objects: 71, done.
remote: Counting objects: 100% (71/71), done.
remote: Compressing objects: 100% (46/46), done.
remote: Total 71 (delta 22), reused 61 (delta 18), pack-reused 0
Unpacking objects: 100% (71/71), done.
$ cd projects/lab06   # Переход в директорию
$ git remote remove origin   # Удаление ссылки на репозиторий
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab06  # Добавление ссылки на репозиторий
```

Добавим переменные в конфигурационный файл для cpack

```ShellSession
$ gsed -i '/project(print)/a\
set(PRINT_VERSION_STRING "v\${PRINT_VERSION}")
' CMakeLists.txt
$ gsed -i '/project(print)/a\
set(PRINT_VERSION\
  \${PRINT_VERSION_MAJOR}.\${PRINT_VERSION_MINOR}.\${PRINT_VERSION_PATCH}.\${PRINT_VERSION_TWEAK})
' CMakeLists.txt
$ gsed -i '/project(print)/a\
set(PRINT_VERSION_TWEAK 0)
' CMakeLists.txt
$ gsed -i '/project(print)/a\
set(PRINT_VERSION_PATCH 0)
' CMakeLists.txt
$ gsed -i '/project(print)/a\
set(PRINT_VERSION_MINOR 1)
' CMakeLists.txt
$ gsed -i '/project(print)/a\
set(PRINT_VERSION_MAJOR 0)
' CMakeLists.txt
$ git diff          # Посмотреть, что изменилось
diff --git a/CMakeLists.txt b/CMakeLists.txt
index aa7a323..71b64e3 100644
--- a/CMakeLists.txt
+++ b/CMakeLists.txt
@@ -7,6 +7,13 @@ option(BUILD_EXAMPLES "Build examples" OFF)
 option(BUILD_TESTS "Build tests" OFF)
 
 project(print)
+set(PRINT_VERSION_MAJOR 0)
+set(PRINT_VERSION_MINOR 1)
+set(PRINT_VERSION_PATCH 0)
+set(PRINT_VERSION_TWEAK 0)
+set(PRINT_VERSION
+  ${PRINT_VERSION_MAJOR}.${PRINT_VERSION_MINOR}.${PRINT_VERSION_PATCH}.${PRINT_VERSION_TWEAK})
+set(PRINT_VERSION_STRING "v${PRINT_VERSION}")
 
 add_library(print STATIC ${CMAKE_CURRENT_SOURCE_DIR}/sources/print.cpp)
 
```

Создание необходимых для пакетирования файлов

```ShellSession
$ touch DESCRIPTION && edit DESCRIPTION         # Создаем описание
$ touch ChangeLog.md            # Создаем список изменений
$ export DATE="`LANG=en_US date +'%a %b %d %Y'`"    # Установка переменной DATE
$ cat > ChangeLog.md <<EOF          # Запись информации об изменениях
* ${DATE} ${GITHUB_USERNAME} <${GITHUB_EMAIL}> 0.1.0.0
- Initial RPM release
EOF
```

Пишем в конфигурационный файл для cpack о том, что нужно поставить необходимые системные библиотеки

```ShellSession
$ cat > CPackConfig.cmake <<EOF
include(InstallRequiredSystemLibraries)
EOF
```

Пишем в конфигурационный файл для cpack основную информацию о пакете

```ShellSession
$ cat >> CPackConfig.cmake <<EOF
set(CPACK_PACKAGE_CONTACT ${GITHUB_EMAIL})
set(CPACK_PACKAGE_VERSION_MAJOR \${PRINT_VERSION_MAJOR})
set(CPACK_PACKAGE_VERSION_MINOR \${PRINT_VERSION_MINOR})
set(CPACK_PACKAGE_VERSION_PATCH \${PRINT_VERSION_PATCH})
set(CPACK_PACKAGE_VERSION_TWEAK \${PRINT_VERSION_TWEAK})
set(CPACK_PACKAGE_VERSION \${PRINT_VERSION})
set(CPACK_PACKAGE_DESCRIPTION_FILE \${CMAKE_CURRENT_SOURCE_DIR}/DESCRIPTION)
set(CPACK_PACKAGE_DESCRIPTION_SUMMARY "static C++ library for printing")
EOF
```

Пишем в конфигурационный файл для cpack путь к лицензии и README

```ShellSession
$ cat >> CPackConfig.cmake <<EOF

set(CPACK_RESOURCE_FILE_LICENSE \${CMAKE_CURRENT_SOURCE_DIR}/LICENSE)
set(CPACK_RESOURCE_FILE_README \${CMAKE_CURRENT_SOURCE_DIR}/README.md)
EOF
```

Пишем в конфигурационный файл для cpack информацию об RPM пакете

```ShellSession
$ cat >> CPackConfig.cmake <<EOF

set(CPACK_RPM_PACKAGE_NAME "print-devel")
set(CPACK_RPM_PACKAGE_LICENSE "MIT")
set(CPACK_RPM_PACKAGE_GROUP "print")
set(CPACK_RPM_CHANGELOG_FILE \${CMAKE_CURRENT_SOURCE_DIR}/ChangeLog.md)
set(CPACK_RPM_PACKAGE_RELEASE 1)
EOF
```

Пишем в конфигурационный файл для cpack информацию о DEB пакете

```ShellSession
$ cat >> CPackConfig.cmake <<EOF

set(CPACK_DEBIAN_PACKAGE_NAME "libprint-dev")
set(CPACK_DEBIAN_PACKAGE_PREDEPENDS "cmake >= 3.0")
set(CPACK_DEBIAN_PACKAGE_RELEASE 1)
EOF
```

Подключение основного cpack конфигурационного файла

```ShellSession
$ cat >> CPackConfig.cmake <<EOF

include(CPack)
EOF
```

Подключим cpack конфигурационный файл в CMakeLists.txt

```ShellSession
$ cat >> CMakeLists.txt <<EOF

include(CPackConfig.cmake)
EOF
```

Замена lab05 на lab06 в README.md

```ShellSession
$ gsed -i 's/lab05/lab06/g' README.md
```

Отправка изменений на гитхаб

```ShellSession
$ git add .     # Фиксируем
$ git commit -m"added cpack config" # Коммитим
[master 9625792] added cpack config
 4 files changed, 36 insertions(+)
 create mode 100644 CPackConfig.cmake
 create mode 100644 ChangeLog.md
 create mode 100644 DESCRIPTION
$ git tag v0.1.0.0  # Добавим тег
$ git push origin master --tags   # Отправим на удаленный репо
```

Включим CI

```ShellSession
$ travis login --auto
Successfully logged in as Dimontich!
$ travis enable
Detected repository as Dimontich/lab06, is this correct? |yes| yes
Dimontich/lab06: enabled :)
```

Пакетирование через CPack

```ShellSession
$ cmake -H. -B_build         # Конфигурирование
-- The C compiler identification is GNU 6.3.0
-- The CXX compiler identification is GNU 6.3.0
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done
-- Generating done
-- Build files have been written to: /Dimontich/workspace/projects/lab06/_build
$ cmake --build _build       # Сборка
Scanning dependencies of target print
[ 50%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
[100%] Linking CXX static library libprint.a
[100%] Built target print
$ cd _build                 # переходим в директорию _build
$ cpack -G "TGZ"            # Создаем пакет (генератор - TGZ)
CPack: Create package using TGZ
CPack: Install projects
CPack: - Run preinstall target for: print
CPack: - Install project: print
CPack: Create package
CPack: - package: /Dimontich/workspace/projects/lab06/_build/print-0.1.0.0-Linux.tar.gz generated.
$ cd ..                     # Переходим на раздел выше
```

Собираем пакет через cmake

```ShellSession
$ cmake -H. -B_build -DCPACK_GENERATOR="TGZ"
-- Configuring done
-- Generating done
-- Build files have been written to: /Dimontich/workspace/projects/lab06/_build
$ cmake --build _build --target package
[100%] Built target print
Run CPack packaging tool...
CPack: Create package using TGZ
CPack: Install projects
CPack: - Run preinstall target for: print
CPack: - Install project: print
CPack: Create package
CPack: - package: /Dimontich/workspace/projects/lab06/_build/print-0.1.0.0-Linux.tar.gz generated.
```

Перемещение пакета в отдельную директорию

```ShellSession
$ mkdir artifacts
$ mv _build/*.tar.gz artifacts
$ tree artifacts
artifacts
`-- print-0.1.0.0-Linux.tar.gz

0 directories, 1 file
```

## Report

```ShellSession
$ popd
$ export LAB_NUMBER=06
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gistup -m "lab${LAB_NUMBER}"
```

## Homework

После того, как вы настроили взаимодействие с системой непрерывной интеграции,</br>
обеспечив автоматическую сборку и тестирование ваших изменений, стоит задуматься</br>
о создание пакетов для измениний, которые помечаются тэгами (см. вкладку [releases](https://github.com/tp-labs/lab06/releases)).</br>
Пакет должен содержать приложение _solver_ из [предыдущего задания](https://github.com/tp-labs/lab03#задание-1)
Таким образом, каждый новый релиз будет состоять из следующих компонентов:
- архивы с файлами исходного кода (`.tar.gz`, `.zip`)
- пакеты с бинарным файлом _solver_ (`.deb`, `.rpm`, `.msi`, `.dmg`)

В качестве подсказки:
```bash
$ cat .travis.yml
os: osx
script:
...
- cpack -G DragNDrop # dmg

$ cat .travis.yml
os: linux
script:
...
- cpack -G DEB # deb

$ cat .travis.yml
os: linux
addons:
  apt:
    packages:
    - rpm
script:
...
- cpack -G RPM # rpm

$ cat appveyor.yml
platform:
- x86
- x64
build_script:
...
- cpack -G WIX # msi
```

Для этого нужно добавить ветвление в конфигурационные файлы для **CI** со следующей логикой:</br>
если **commit** помечен тэгом, то необходимо собрать пакеты (`DEB, RPM, WIX, DragNDrop, ...`) </br>
и разместить их на сервисе **GitHub**. (см. пример для [Travi CI](https://docs.travis-ci.com/user/deployment/releases))</br>

## Links

- [DMG](https://cmake.org/cmake/help/latest/module/CPackDMG.html)
- [DEB](https://cmake.org/cmake/help/latest/module/CPackDeb.html)
- [RPM](https://cmake.org/cmake/help/latest/module/CPackRPM.html)
- [NSIS](https://cmake.org/cmake/help/latest/module/CPackNSIS.html)

```
Copyright (c) 2015-2019 The ISC Authors
```
