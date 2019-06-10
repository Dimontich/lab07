[![Build Status](https://travis-ci.org/Dimontich/lab05.svg?branch=master)](https://travis-ci.org/Dimontich/lab05)

## Laboratory work V

Данная лабораторная работа посвещена изучению фреймворков для тестирования на примере **GTest**

```ShellSession
$ open https://github.com/google/googletest
```

## Tasks

- [x] 1. Создать публичный репозиторий с названием **lab05** на сервисе **GitHub**
- [x] 2. Выполнить инструкцию учебного материала
- [x] 3. Ознакомиться со ссылками учебного материала
- [x] 4. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Tutorial

Установка переменных

```ShellSession
$ export GITHUB_USERNAME=Dimontich  # Установка переменной GITHUB_USERNAME
$ alias gsed=sed # for *-nix system     # Установка команды gsed
```

Подготовка

```ShellSession
$ cd ${GITHUB_USERNAME}/workspace   # Переход в директорию workspace
$ pushd .         # Сохранение директории 
/Dimontich/workspace /Dimontich/workspace
$ source scripts/activate       # Выполнение скрипта 
```

```ShellSession
$ git clone https://github.com/${GITHUB_USERNAME}/lab04 projects/lab05  # Клонирование репо
Cloning into 'projects/lab05'...
remote: Enumerating objects: 51, done.
remote: Counting objects: 100% (51/51), done.
remote: Compressing objects: 100% (35/35), done.
remote: Total 51 (delta 13), reused 47 (delta 12), pack-reused 0
Unpacking objects: 100% (51/51), done.
$ cd projects/lab05   # Переход в директорию
$ git remote remove origin    # Удаление ссылки на репозиторий
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab05  # Добавление ссылки на репозиторий
```

Скачиваем библиотеку GTest

```ShellSession
$ mkdir third-party             # Создание директории
$ git submodule add https://github.com/google/googletest third-party/gtest   # Добавление подмодуля git
Cloning into '/Dimontich/workspace/projects/lab05/third-party/gtest'...
remote: Enumerating objects: 16892, done.
remote: Total 16892 (delta 0), reused 0 (delta 0), pack-reused 16892
Receiving objects: 100% (16892/16892), 5.96 MiB | 4.62 MiB/s, done.
Resolving deltas: 100% (12445/12445), done.
$ cd third-party/gtest && git checkout release-1.8.1 && cd ../..        # Переход на тэг release-1.8.1 в подмодуле git
Note: checking out 'release-1.8.1'.

You are in 'detached HEAD' state. You can look around, make experimental
changes and commit them, and you can discard any commits you make in this
state without impacting any branches by performing another checkout.

If you want to create a new branch to retain commits you create, you may
do so (now or later) by using -b with the checkout command again. Example:

  git checkout -b <new-branch-name>

HEAD is now at 2fe3bd99... Merge pull request #1433 from dsacre/fix-clang-warnings
$ git add third-party/gtest                 # Фиксируем
$ git commit -m"added gtest framework"      # Коммитим
[master ead2839] added gtest framework
 2 files changed, 4 insertions(+)
 create mode 100644 .gitmodules
 create mode 160000 third-party/gtest
```

Добавление GTest в конфигурацию проекта

```ShellSession
# Добавление опции BUILD_TESTS (по умолчанию выкл)
$ gsed -i '/option(BUILD_EXAMPLES "Build examples" OFF)/a\
option(BUILD_TESTS "Build tests" OFF)
' CMakeLists.txt

# Добавление блока с тестами
$ cat >> CMakeLists.txt <<EOF

if(BUILD_TESTS)
  enable_testing()
  add_subdirectory(third-party/gtest)
  file(GLOB \${PROJECT_NAME}_TEST_SOURCES tests/*.cpp)
  add_executable(check \${\${PROJECT_NAME}_TEST_SOURCES})
  target_link_libraries(check \${PROJECT_NAME} gtest_main)
  add_test(NAME check COMMAND check)
endif()
EOF
```

Исходный код тестов

```ShellSession
$ mkdir tests           # Создание директории с тестами
$ cat > tests/test1.cpp <<EOF       # Пишем код
#include <print.hpp>

#include <gtest/gtest.h>

TEST(Print, InFileStream)
{
  std::string filepath = "file.txt";
  std::string text = "hello";
  std::ofstream out{filepath};

  print(text, out);
  out.close();

  std::string result;
  std::ifstream in{filepath};
  in >> result;

  EXPECT_EQ(result, text);
}
EOF
```

Сборка с тестами

```ShellSession
$ cmake -H. -B_build -DBUILD_TESTS=ON   # Конфигурирование
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
-- Could NOT find PythonInterp (missing:  PYTHON_EXECUTABLE) 
-- Looking for pthread.h
-- Looking for pthread.h - found
-- Looking for pthread_create
-- Looking for pthread_create - not found
-- Check if compiler accepts -pthread
-- Check if compiler accepts -pthread - yes
-- Found Threads: TRUE  
-- Configuring done
-- Generating done
-- Build files have been written to: /Dimontich/workspace/projects/lab05/_build
$ cmake --build _build                  # Сборка
Scanning dependencies of target print
[  8%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
[ 16%] Linking CXX static library libprint.a
[ 16%] Built target print
Scanning dependencies of target gtest
[ 25%] Building CXX object third-party/gtest/googlemock/gtest/CMakeFiles/gtest.dir/src/gtest-all.cc.o
[ 33%] Linking CXX static library libgtest.a
[ 33%] Built target gtest
Scanning dependencies of target gtest_main
[ 41%] Building CXX object third-party/gtest/googlemock/gtest/CMakeFiles/gtest_main.dir/src/gtest_main.cc.o
[ 50%] Linking CXX static library libgtest_main.a
[ 50%] Built target gtest_main
Scanning dependencies of target check
[ 58%] Building CXX object CMakeFiles/check.dir/tests/test1.cpp.o
[ 66%] Linking CXX executable check
[ 66%] Built target check
Scanning dependencies of target gmock
[ 75%] Building CXX object third-party/gtest/googlemock/CMakeFiles/gmock.dir/src/gmock-all.cc.o
[ 83%] Linking CXX static library libgmock.a
[ 83%] Built target gmock
Scanning dependencies of target gmock_main
[ 91%] Building CXX object third-party/gtest/googlemock/CMakeFiles/gmock_main.dir/src/gmock_main.cc.o
[100%] Linking CXX static library libgmock_main.a
[100%] Built target gmock_main
$ cmake --build _build --target test    # Тесты
Running tests...
Test project /Dimontich/workspace/projects/lab05/_build
    Start 1: check
1/1 Test #1: check ............................   Passed    0.00 sec

100% tests passed, 0 tests failed out of 1

Total Test time (real) =   0.01 sec
```

Подробное выполнение тестов

```ShellSession
$ _build/check      # Исполняемый файл
Running main() from /Dimontich/workspace/projects/lab05/third-party/gtest/googletest/src/gtest_main.cc
[==========] Running 1 test from 1 test case.
[----------] Global test environment set-up.
[----------] 1 test from Print
[ RUN      ] Print.InFileStream
[       OK ] Print.InFileStream (1 ms)
[----------] 1 test from Print (1 ms total)

[----------] Global test environment tear-down
[==========] 1 test from 1 test case ran. (1 ms total)
[  PASSED  ] 1 test.
$ cmake --build _build --target test -- ARGS=--verbose   # Тесты с подробным описанием
Running tests...
UpdateCTestConfiguration  from :/Dimontich/workspace/projects/lab05/_build/DartConfiguration.tcl
UpdateCTestConfiguration  from :/Dimontich/workspace/projects/lab05/_build/DartConfiguration.tcl
Test project /Dimontich/workspace/projects/lab05/_build
Constructing a list of tests
Done constructing a list of tests
Updating test list for fixtures
Added 0 tests to meet fixture requirements
Checking test dependency graph...
Checking test dependency graph end
test 1
    Start 1: check

1: Test command: /Dimontich/workspace/projects/lab05/_build/check
1: Test timeout computed to be: 9.99988e+06
1: Running main() from /Dimontich/workspace/projects/lab05/third-party/gtest/googletest/src/gtest_main.cc
1: [==========] Running 1 test from 1 test case.
1: [----------] Global test environment set-up.
1: [----------] 1 test from Print
1: [ RUN      ] Print.InFileStream
1: [       OK ] Print.InFileStream (1 ms)
1: [----------] 1 test from Print (1 ms total)
1: 
1: [----------] Global test environment tear-down
1: [==========] 1 test from 1 test case ran. (1 ms total)
1: [  PASSED  ] 1 test.
1/1 Test #1: check ............................   Passed    0.00 sec

100% tests passed, 0 tests failed out of 1

Total Test time (real) =   0.01 sec
```

Обновление конфигурационного файла travis

```ShellSession
$ gsed -i 's/lab04/lab05/g' README.md
$ gsed -i 's/\(DCMAKE_INSTALL_PREFIX=_install\)/\1 -DBUILD_TESTS=ON/' .travis.yml
$ gsed -i '/cmake --build _build --target install/a\
- cmake --build _build --target test -- ARGS=--verbose
' .travis.yml
```

Анализ конфигурационного файла travis

```ShellSession
$ travis lint
Warnings for .travis.yml:
[x] value for addons section is empty, dropping
[x] in addons section: unexpected key apt, dropping
```

Отправка изменений

```ShellSession
$ git add .travis.yml       # Фиксация
$ git add tests             # Фиксация
$ git add -p                # Интерактивная фиксация
$ git commit -m"added tests"    # Коммит
[master b4e9de6] added tests
 4 files changed, 41 insertions(+), 11 deletions(-)
 create mode 100644 tests/test1.cpp
$ git push origin master    # Отправляем
Counting objects: 62, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (56/56), done.
Writing objects: 100% (62/62), 25.33 KiB | 0 bytes/s, done.
Total 62 (delta 18), reused 0 (delta 0)
remote: Resolving deltas: 100% (18/18), done.
To https://github.com/Dimontich/lab05
 * [new branch]      master -> master
```

```ShellSession
$ travis login --auto       # Авторизируемся
Successfully logged in as Dimontich!
$ travis enable             # Активируем CI
Dimontich/lab05: enabled :)
```

Сохранение скриншота с результатом

```ShellSession
$ mkdir artifacts
$ sleep 20s && gnome-screenshot --file artifacts/screenshot.png
# for macOS: $ screencapture -T 20 artifacts/screenshot.png
# open https://github.com/${GITHUB_USERNAME}/lab05
```

## Report

```ShellSession
$ popd
$ export LAB_NUMBER=05
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gistup -m "lab${LAB_NUMBER}"
```

## Homework

### Задание
1. Создайте `CMakeList.txt` для библиотеки *banking*.
2. Создайте модульные тесты на классы `Transaction` и `Account`.
    * Используйте mock-объекты.
    * Покрытие кода должно составлять 100%.
3. Настройте сборочную процедуру на **TravisCI**.
4. Настройте [Coveralls.io](https://coveralls.io/).

## Links

- [C++ CI: Travis, CMake, GTest, Coveralls & Appveyor](http://david-grs.github.io/cpp-clang-travis-cmake-gtest-coveralls-appveyor/)
- [Boost.Tests](http://www.boost.org/doc/libs/1_63_0/libs/test/doc/html/)
- [Catch](https://github.com/catchorg/Catch2)

```
Copyright (c) 2015-2019 The ISC Authors
```
