[![Build Status](https://travis-ci.org/Dimontich/lab07.svg?branch=master)](https://travis-ci.org/Dimontich/lab07)

## Laboratory work VII

Данная лабораторная работа посвещена изучению систем управления пакетами на примере **Hunter**

```ShellSession
$ open https://github.com/ruslo/hunter
```

## Tasks

- [x] 1. Создать публичный репозиторий с названием **lab07** на сервисе **GitHub**
- [x] 2. Выполнить инструкцию учебного материала
- [x] 3. Ознакомиться со ссылками учебного материала
- [x] 4. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Tutorial

Установка переменных

```ShellSession
$ export GITHUB_USERNAME=<имя_пользователя>  # Установка переменной GITHUB_USERNAME
```

Подготовка

```ShellSession
$ cd ${GITHUB_USERNAME}/workspace   # Переход в директорию workspace
$ pushd .   # Сохранение директории
$ source scripts/activate    # Выполнение скрипта
```

Подготовка файлов

```ShellSession
$ git clone https://github.com/${GITHUB_USERNAME}/lab06 projects/lab07  # Клонирование репо
Cloning into 'projects/lab07'...
remote: Enumerating objects: 80, done.
remote: Counting objects: 100% (80/80), done.
remote: Compressing objects: 100% (47/47), done.
remote: Total 80 (delta 26), reused 76 (delta 25), pack-reused 0
Unpacking objects: 100% (80/80), done.
$ cd projects/lab07   # Переход в директорию
$ git remote remove origin      # Удаление ссылки на репозиторий
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab07  # Добавление ссылки на репозиторий
```

Установка hunter-gate

```ShellSession
$ wget https://github.com/hunter-packages/gate/archive/v0.9.0.tar.gz -O /tmp/gate.tar.gz  # Скачиваем gate
$ tar -xf /tmp/gate.tar.gz    # Разархивируем
$ mkdir -p cmake        # Создаем директорию
$ mv gate-0.9.0/cmake/HunterGate.cmake cmake     # Перемещаем
$ rm -rf gate-0.9.0             # Удаляем директорию
$ gsed -i '/cmake_minimum_required(VERSION 3.0)/a\      # Добавляем в CMakeLists.txt информацию о hunter
\
include("cmake/HunterGate.cmake")\
huntergate(\
  URL "https://github.com/ruslo/hunter/archive/v0.23.83.tar.gz"\
  SHA1 "12dec078717539eb7b03e6d2a17797cba9be9ba9"\
)
' CMakeLists.txt
```

Конфигурирование CMake с hunter

```ShellSession
$ git rm -rf third-party/gtest          # Удалим директорию
$ gsed -i '/set(PRINT_VERSION_STRING "v\${PRINT_VERSION}")/a\
\
hunter_add_package(GTest)\
find_package(GTest CONFIG REQUIRED)
' CMakeLists.txt
$ gsed -i 's/add_subdirectory(third-party\/gtest)//' CMakeLists.txt # Добавление поддиректории CMake
$ gsed -i 's/gtest_main/GTest::main/' CMakeLists.txt            # Линковка новой библиотеки
```

Сборка через CMake

```ShellSession
$ cmake -H. -B_builds -DBUILD_TESTS=ON      # Конфигурирование
-- [hunter] Initializing Hunter workspace (12dec078717539eb7b03e6d2a17797cba9be9ba9)
-- [hunter]   https://github.com/ruslo/hunter/archive/v0.23.83.tar.gz
-- [hunter]   -> /root/.hunter/_Base/Download/Hunter/0.23.83/12dec07
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
-- [hunter] Calculating Toolchain-SHA1
-- [hunter] Calculating Config-SHA1
# Большой вывод
-- Installing: /root/.hunter/_Base/12dec07/dffde48/510d5e8/Build/GTest/Install/lib/cmake/GTest/GTestTargets.cmake
-- Installing: /root/.hunter/_Base/12dec07/dffde48/510d5e8/Build/GTest/Install/lib/cmake/GTest/GTestTargets-debug.cmake
loading initial cache file /root/.hunter/_Base/12dec07/dffde48/510d5e8/Build/GTest/args.cmake
[100%] Completed 'GTest-Debug'
[100%] Built target GTest-Debug
-- [hunter] Build step successful (dir: /root/.hunter/_Base/12dec07/dffde48/510d5e8/Build/GTest)
-- [hunter] Cache saved: /root/.hunter/_Base/Cache/raw/83f8b575041da969def695d58d4baef1fde15ea0.tar.bz2
-- Configuring done
-- Generating done
-- Build files have been written to: /Dimontich/workspace/projects/lab07/_builds
$ cmake --build _builds         # Сборка
Scanning dependencies of target print
[ 25%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
[ 50%] Linking CXX static library libprint.a
[ 50%] Built target print
Scanning dependencies of target check
[ 75%] Building CXX object CMakeFiles/check.dir/tests/test1.cpp.o
[100%] Linking CXX executable check
[100%] Built target check
$ cmake --build _builds --target test   # Проверим тесты
Running tests...
Test project /Dimontich/workspace/projects/lab07/_builds
    Start 1: check
1/1 Test #1: check ............................   Passed    0.01 sec

100% tests passed, 0 tests failed out of 1

Total Test time (real) =   0.01 sec
$ ls -la $HOME/.hunter              # Что лежит в директории .hunter
total 16
drwxr-xr-x 3 root root 4096 Jun 10 14:48 .
drwx------ 1 root root 4096 Jun 10 14:48 ..
drwxr-xr-x 6 root root 4096 Jun 10 14:48 _Base
```

Установка hunter

```ShellSession
$ git clone https://github.com/ruslo/hunter $HOME/projects/hunter       # клонируем репо
Cloning into '/root/projects/hunter'...
remote: Enumerating objects: 41702, done.
remote: Total 41702 (delta 0), reused 0 (delta 0), pack-reused 41702
Receiving objects: 100% (41702/41702), 10.62 MiB | 5.41 MiB/s, done.
Resolving deltas: 100% (26234/26234), done.
$ export HUNTER_ROOT=$HOME/projects/hunter          # устанавливаем переменную HUNTER_ROOT
$ rm -rf _builds            # Очистка сборки
$ cmake -H. -B_builds -DBUILD_TESTS=ON      # Конфигурирование
-- [hunter] Initializing Hunter workspace (12dec078717539eb7b03e6d2a17797cba9be9ba9)
-- [hunter]   https://github.com/ruslo/hunter/archive/v0.23.83.tar.gz
-- [hunter]   -> /root/projects/hunter/_Base/Download/Hunter/0.23.83/12dec07
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
-- [hunter] Calculating Toolchain-SHA1
-- [hunter] Calculating Config-SHA1
-- [hunter] HUNTER_ROOT: /root/projects/hunter
-- [hunter] [ Hunter-ID: 12dec07 | Toolchain-ID: dffde48 | Config-ID: cedd094 ]
-- [hunter] GTEST_ROOT: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Install (ver.: 1.8.0-hunter-p11)
-- [hunter] Building GTest
loading initial cache file /root/projects/hunter/_Base/12dec07/dffde48/cedd094/cache.cmake
loading initial cache file /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/args.cmake
-- The C compiler identification is GNU 6.3.0
-- The CXX compiler identification is GNU 6.3.0
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Configuring done
-- Generating done
-- Build files have been written to: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Build
Scanning dependencies of target GTest-Release
[  6%] Creating directories for 'GTest-Release'
[ 12%] Performing download step (download, verify and extract) for 'GTest-Release'
-- Downloading...
   dst='/root/projects/hunter/_Base/Download/GTest/1.8.0-hunter-p11/76c6aec/1.8.0-hunter-p11.tar.gz'
   timeout='none'
-- Using src='https://github.com/hunter-packages/googletest/archive/1.8.0-hunter-p11.tar.gz'
-- verifying file...
       file='/root/projects/hunter/_Base/Download/GTest/1.8.0-hunter-p11/76c6aec/1.8.0-hunter-p11.tar.gz'
-- Downloading... done
-- extracting...
     src='/root/projects/hunter/_Base/Download/GTest/1.8.0-hunter-p11/76c6aec/1.8.0-hunter-p11.tar.gz'
     dst='/root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Source'
-- extracting... [tar xfz]
-- extracting... [analysis]
-- extracting... [rename]
-- extracting... [clean up]
-- extracting... done
[ 18%] No patch step for 'GTest-Release'
[ 25%] No update step for 'GTest-Release'
[ 31%] Performing configure step for 'GTest-Release'
loading initial cache file /root/projects/hunter/_Base/12dec07/dffde48/cedd094/cache.cmake
loading initial cache file /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/args.cmake
-- The C compiler identification is GNU 6.3.0
-- The CXX compiler identification is GNU 6.3.0
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
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
-- Build files have been written to: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Build/GTest-Release-prefix/src/GTest-Release-build
[ 37%] Performing build step for 'GTest-Release'
Scanning dependencies of target gmock_main
Scanning dependencies of target gmock
Scanning dependencies of target gtest
[  9%] Building CXX object googlemock/CMakeFiles/gmock.dir/__/googletest/src/gtest-all.cc.o
[ 18%] Building CXX object googlemock/CMakeFiles/gmock_main.dir/src/gmock-all.cc.o
[ 45%] Building CXX object googlemock/gtest/CMakeFiles/gtest.dir/src/gtest-all.cc.o
[ 45%] Building CXX object googlemock/CMakeFiles/gmock.dir/src/gmock-all.cc.o
[ 45%] Building CXX object googlemock/CMakeFiles/gmock_main.dir/__/googletest/src/gtest-all.cc.o
[ 54%] Building CXX object googlemock/CMakeFiles/gmock_main.dir/src/gmock_main.cc.o
[ 63%] Linking CXX static library libgtest.a
[ 63%] Built target gtest
Scanning dependencies of target gtest_main
[ 72%] Linking CXX static library libgmock_main.a
[ 81%] Building CXX object googlemock/gtest/CMakeFiles/gtest_main.dir/src/gtest_main.cc.o
[ 81%] Built target gmock_main
[ 90%] Linking CXX static library libgmock.a
[ 90%] Built target gmock
[100%] Linking CXX static library libgtest_main.a
[100%] Built target gtest_main
[ 43%] Performing install step for 'GTest-Release'
[ 36%] Built target gmock_main
[ 63%] Built target gmock
[ 81%] Built target gtest
[100%] Built target gtest_main
Install the project...
-- Install configuration: "Release"
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/lib/libgmock.a
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/lib/libgmock_main.a
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gmock
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gmock/internal
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gmock/internal/custom
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gmock/internal/custom/gmock-matchers.h
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gmock/internal/custom/gmock-generated-actions.h
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gmock/internal/custom/gmock-port.h
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gmock/internal/gmock-port.h
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gmock/internal/gmock-generated-internal-utils.h
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gmock/internal/gmock-internal-utils.h
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gmock/gmock-cardinalities.h
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gmock/gmock-more-actions.h
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gmock/gmock-matchers.h
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gmock/gmock-generated-actions.h
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gmock/gmock.h
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gmock/gmock-generated-function-mockers.h
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gmock/gmock-generated-matchers.h
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gmock/gmock-spec-builders.h
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gmock/gmock-generated-nice-strict.h
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gmock/gmock-actions.h
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gmock/gmock-more-matchers.h
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/lib/cmake/GMock/GMockConfig.cmake
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/lib/cmake/GMock/GMockConfigVersion.cmake
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/lib/cmake/GMock/GMockTargets.cmake
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/lib/cmake/GMock/GMockTargets-release.cmake
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/lib/libgtest.a
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/lib/libgtest_main.a
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/gtest-spi.h
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/internal
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/internal/gtest-port-arch.h
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/internal/gtest-death-test-internal.h
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/internal/gtest-type-util.h
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/internal/gtest-filepath.h
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/internal/custom
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/internal/custom/gtest-port.h
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/internal/custom/gtest-printers.h
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/internal/custom/gtest.h
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/internal/gtest-internal.h
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/internal/gtest-param-util-generated.h
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/internal/gtest-tuple.h
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/internal/gtest-param-util.h
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/internal/gtest-port.h
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/internal/gtest-string.h
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/internal/gtest-linked_ptr.h
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/gtest-message.h
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/gtest-death-test.h
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/gtest-param-test.h
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/gtest_prod.h
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/gtest-printers.h
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/gtest_pred_impl.h
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/gtest-test-part.h
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/gtest.h
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/gtest-typed-test.h
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/lib/cmake/GTest/GTestConfig.cmake
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/lib/cmake/GTest/GTestConfigVersion.cmake
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/lib/cmake/GTest/GTestTargets.cmake
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/lib/cmake/GTest/GTestTargets-release.cmake
loading initial cache file /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/args.cmake
[ 50%] Completed 'GTest-Release'
[ 50%] Built target GTest-Release
Scanning dependencies of target GTest-Debug
[ 56%] Creating directories for 'GTest-Debug'
[ 62%] Performing download step (download, verify and extract) for 'GTest-Debug'
-- verifying file...
       file='/root/projects/hunter/_Base/Download/GTest/1.8.0-hunter-p11/76c6aec/1.8.0-hunter-p11.tar.gz'
-- File already exists and hash match (skip download):
  file='/root/projects/hunter/_Base/Download/GTest/1.8.0-hunter-p11/76c6aec/1.8.0-hunter-p11.tar.gz'
  SHA1='76c6aec038f7d7258bf5c4f45c4817b34039d285'
-- extracting...
     src='/root/projects/hunter/_Base/Download/GTest/1.8.0-hunter-p11/76c6aec/1.8.0-hunter-p11.tar.gz'
     dst='/root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Source'
-- extracting... [tar xfz]
-- extracting... [analysis]
-- extracting... [rename]
-- extracting... [clean up]
-- extracting... done
[ 68%] No patch step for 'GTest-Debug'
[ 75%] No update step for 'GTest-Debug'
[ 81%] Performing configure step for 'GTest-Debug'
loading initial cache file /root/projects/hunter/_Base/12dec07/dffde48/cedd094/cache.cmake
loading initial cache file /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/args.cmake
-- The C compiler identification is GNU 6.3.0
-- The CXX compiler identification is GNU 6.3.0
-- Check for working C compiler: /usr/bin/cc
-- Check for working C compiler: /usr/bin/cc -- works
-- Detecting C compile features
-- Detecting C compile features - done
-- Check for working CXX compiler: /usr/bin/c++
-- Check for working CXX compiler: /usr/bin/c++ -- works
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
-- Build files have been written to: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Build/GTest-Debug-prefix/src/GTest-Debug-build
[ 87%] Performing build step for 'GTest-Debug'
Scanning dependencies of target gtest
Scanning dependencies of target gmock_main
Scanning dependencies of target gmock
[  9%] Building CXX object googlemock/gtest/CMakeFiles/gtest.dir/src/gtest-all.cc.o
[ 18%] Building CXX object googlemock/CMakeFiles/gmock.dir/__/googletest/src/gtest-all.cc.o
[ 27%] Building CXX object googlemock/CMakeFiles/gmock_main.dir/__/googletest/src/gtest-all.cc.o
[ 36%] Building CXX object googlemock/CMakeFiles/gmock_main.dir/src/gmock-all.cc.o
[ 54%] Building CXX object googlemock/CMakeFiles/gmock.dir/src/gmock-all.cc.o
[ 54%] Building CXX object googlemock/CMakeFiles/gmock_main.dir/src/gmock_main.cc.o
[ 63%] Linking CXX static library libgmock_maind.a
[ 63%] Built target gmock_main
[ 72%] Linking CXX static library libgtestd.a
[ 72%] Built target gtest
Scanning dependencies of target gtest_main
[ 81%] Building CXX object googlemock/gtest/CMakeFiles/gtest_main.dir/src/gtest_main.cc.o
[ 90%] Linking CXX static library libgmockd.a
[ 90%] Built target gmock
[100%] Linking CXX static library libgtest_maind.a
[100%] Built target gtest_main
[ 93%] Performing install step for 'GTest-Debug'
[ 36%] Built target gmock_main
[ 63%] Built target gmock
[ 81%] Built target gtest
[100%] Built target gtest_main
Install the project...
-- Install configuration: "Debug"
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/lib/libgmockd.a
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/lib/libgmock_maind.a
-- Up-to-date: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gmock
-- Up-to-date: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gmock/internal
-- Up-to-date: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gmock/internal/custom
-- Up-to-date: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gmock/internal/custom/gmock-matchers.h
-- Up-to-date: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gmock/internal/custom/gmock-generated-actions.h
-- Up-to-date: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gmock/internal/custom/gmock-port.h
-- Up-to-date: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gmock/internal/gmock-port.h
-- Up-to-date: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gmock/internal/gmock-generated-internal-utils.h
-- Up-to-date: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gmock/internal/gmock-internal-utils.h
-- Up-to-date: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gmock/gmock-cardinalities.h
-- Up-to-date: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gmock/gmock-more-actions.h
-- Up-to-date: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gmock/gmock-matchers.h
-- Up-to-date: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gmock/gmock-generated-actions.h
-- Up-to-date: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gmock/gmock.h
-- Up-to-date: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gmock/gmock-generated-function-mockers.h
-- Up-to-date: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gmock/gmock-generated-matchers.h
-- Up-to-date: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gmock/gmock-spec-builders.h
-- Up-to-date: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gmock/gmock-generated-nice-strict.h
-- Up-to-date: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gmock/gmock-actions.h
-- Up-to-date: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gmock/gmock-more-matchers.h
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/lib/cmake/GMock/GMockConfig.cmake
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/lib/cmake/GMock/GMockConfigVersion.cmake
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/lib/cmake/GMock/GMockTargets.cmake
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/lib/cmake/GMock/GMockTargets-debug.cmake
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/lib/libgtestd.a
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/lib/libgtest_maind.a
-- Up-to-date: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest
-- Up-to-date: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/gtest-spi.h
-- Up-to-date: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/internal
-- Up-to-date: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/internal/gtest-port-arch.h
-- Up-to-date: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/internal/gtest-death-test-internal.h
-- Up-to-date: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/internal/gtest-type-util.h
-- Up-to-date: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/internal/gtest-filepath.h
-- Up-to-date: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/internal/custom
-- Up-to-date: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/internal/custom/gtest-port.h
-- Up-to-date: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/internal/custom/gtest-printers.h
-- Up-to-date: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/internal/custom/gtest.h
-- Up-to-date: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/internal/gtest-internal.h
-- Up-to-date: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/internal/gtest-param-util-generated.h
-- Up-to-date: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/internal/gtest-tuple.h
-- Up-to-date: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/internal/gtest-param-util.h
-- Up-to-date: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/internal/gtest-port.h
-- Up-to-date: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/internal/gtest-string.h
-- Up-to-date: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/internal/gtest-linked_ptr.h
-- Up-to-date: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/gtest-message.h
-- Up-to-date: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/gtest-death-test.h
-- Up-to-date: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/gtest-param-test.h
-- Up-to-date: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/gtest_prod.h
-- Up-to-date: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/gtest-printers.h
-- Up-to-date: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/gtest_pred_impl.h
-- Up-to-date: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/gtest-test-part.h
-- Up-to-date: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/gtest.h
-- Up-to-date: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/include/gtest/gtest-typed-test.h
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/lib/cmake/GTest/GTestConfig.cmake
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/lib/cmake/GTest/GTestConfigVersion.cmake
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/lib/cmake/GTest/GTestTargets.cmake
-- Installing: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/Install/lib/cmake/GTest/GTestTargets-debug.cmake
loading initial cache file /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest/args.cmake
[100%] Completed 'GTest-Debug'
[100%] Built target GTest-Debug
-- [hunter] Build step successful (dir: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Build/GTest)
-- [hunter] Cache saved: /root/projects/hunter/_Base/Cache/raw/6716fd6bd566963103721bd4bb19f96cc48e187b.tar.bz2
-- Configuring done
-- Generating done
-- Build files have been written to: /Dimontich/workspace/projects/lab07/_builds
$ cmake --build _builds             # Сборка
Scanning dependencies of target print
[ 25%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
[ 50%] Linking CXX static library libprint.a
[ 50%] Built target print
Scanning dependencies of target check
[ 75%] Building CXX object CMakeFiles/check.dir/tests/test1.cpp.o
[100%] Linking CXX executable check
[100%] Built target check
$ cmake --build _builds --target test       # Выполнение тестов
Running tests...
Test project /Dimontich/workspace/projects/lab07/_builds
    Start 1: check
1/1 Test #1: check ............................   Passed    0.00 sec

100% tests passed, 0 tests failed out of 1

Total Test time (real) =   0.00 sec
```

```ShellSession
$ cat $HUNTER_ROOT/cmake/configs/default.cmake | grep GTest     # Поиск GTest в default.cmake
hunter_default_version(GTest VERSION 1.7.0-hunter-6)
  hunter_default_version(GTest VERSION 1.8.0-hunter-p11)
$ cat $HUNTER_ROOT/cmake/projects/GTest/hunter.cmake            # Просмотри hunter.cmake
# Copyright (c) 2013, Ruslan Baratov
# All rights reserved.

# !!! DO NOT PLACE HEADER GUARDS HERE !!!

include(hunter_add_version)
include(hunter_cacheable)
include(hunter_download)
include(hunter_pick_scheme)
include(hunter_cmake_args)

hunter_add_version(
    PACKAGE_NAME
    GTest
    VERSION
    "1.7.0-hunter"
    URL
    "https://github.com/hunter-packages/gtest/archive/v1.7.0-hunter.tar.gz"
    SHA1
    1ed1c26d11fb592056c1cb912bd3c784afa96eaa
)

hunter_add_version(
    PACKAGE_NAME
    GTest
    VERSION
    "1.7.0-hunter-1"
    URL
    "https://github.com/hunter-packages/gtest/archive/v1.7.0-hunter-1.tar.gz"
    SHA1
    0cb1dcf75e144ad052d3f1e4923a7773bf9b494f
)

hunter_add_version(
    PACKAGE_NAME
    GTest
    VERSION
    "1.7.0-hunter-2"
    URL
    "https://github.com/hunter-packages/gtest/archive/v1.7.0-hunter-2.tar.gz"
    SHA1
    e62b2ef70308f63c32c560f7b6e252442eed4d57
)

hunter_add_version(
    PACKAGE_NAME
    GTest
    VERSION
    "1.7.0-hunter-3"
    URL
    "https://github.com/hunter-packages/gtest/archive/v1.7.0-hunter-3.tar.gz"
    SHA1
    fea7d3020e20f059255484c69755753ccadf6362
)

hunter_add_version(
    PACKAGE_NAME
    GTest
    VERSION
    "1.7.0-hunter-4"
    URL
    "https://github.com/hunter-packages/gtest/archive/v1.7.0-hunter-4.tar.gz"
    SHA1
    9b439c0c25437a083957b197ac6905662a5d901b
)

hunter_add_version(
    PACKAGE_NAME
    GTest
    VERSION
    "1.7.0-hunter-5"
    URL
    "https://github.com/hunter-packages/gtest/archive/v1.7.0-hunter-5.tar.gz"
    SHA1
    796804df3facb074087a4d8ba6f652e5ac69ad7f
)

hunter_add_version(
    PACKAGE_NAME
    GTest
    VERSION
    "1.7.0-hunter-6"
    URL
    "https://github.com/hunter-packages/gtest/archive/v1.7.0-hunter-6.tar.gz"
    SHA1
    64b93147abe287da8fe4e18cfd54ba9297dafb82
)

hunter_add_version(
    PACKAGE_NAME
    GTest
    VERSION
    "1.7.0-hunter-7"
    URL
    "https://github.com/hunter-packages/gtest/archive/v1.7.0-hunter-7.tar.gz"
    SHA1
    19b5c98747768bcd0622714f2ed40f17aee406b2
)

hunter_add_version(
    PACKAGE_NAME
    GTest
    VERSION
    "1.7.0-hunter-8"
    URL
    "https://github.com/hunter-packages/gtest/archive/v1.7.0-hunter-8.tar.gz"
    SHA1
    ac4d2215aa1b1d745a096e5e3b2dbe0c0f229ea5
)

hunter_add_version(
    PACKAGE_NAME
    GTest
    VERSION
    "1.7.0-hunter-9"
    URL
    "https://github.com/hunter-packages/gtest/archive/v1.7.0-hunter-9.tar.gz"
    SHA1
    8a47fe9c4e550f4ed0e2c05388dd291a059223d9
)

hunter_add_version(
    PACKAGE_NAME
    GTest
    VERSION
    "1.7.0-hunter-10"
    URL
    "https://github.com/hunter-packages/gtest/archive/v1.7.0-hunter-10.tar.gz"
    SHA1
    374e6dbe8619ab467c6b1a0b470a598407b172e9
)

hunter_add_version(
    PACKAGE_NAME
    GTest
    VERSION
    "1.7.0-hunter-11"
    URL
    "https://github.com/hunter-packages/gtest/archive/v1.7.0-hunter-11.tar.gz"
    SHA1
    c6ae948ca2bea1d734af01b1069491b00933ed31
)

hunter_add_version(
    PACKAGE_NAME
    GTest
    VERSION
    1.8.0-hunter-p2
    URL
    "https://github.com/hunter-packages/googletest/archive/1.8.0-hunter-p2.tar.gz"
    SHA1
    93148cb8850abe78b76ed87158fdb6b9c48e38c4
)

hunter_add_version(
    PACKAGE_NAME
    GTest
    VERSION
    1.8.0-hunter-p5
    URL https://github.com/hunter-packages/googletest/archive/1.8.0-hunter-p5.tar.gz
    SHA1 3325aa4fc8b30e665c9f73a60f19387b7db36f85
)

hunter_add_version(
    PACKAGE_NAME
    GTest
    VERSION
    1.8.0-hunter-p6
    URL
    "https://github.com/hunter-packages/googletest/archive/1.8.0-hunter-p6.tar.gz"
    SHA1
    f57096bd01c6f8cbef043b312d4d1e82f29648b6
)

hunter_add_version(
    PACKAGE_NAME
    GTest
    VERSION
    1.8.0-hunter-p7
    URL
    "https://github.com/hunter-packages/googletest/archive/1.8.0-hunter-p7.tar.gz"
    SHA1
    4fe083a96d7597f7dce6f453dca01e1d94a1e45b
)

hunter_add_version(
    PACKAGE_NAME
    GTest
    VERSION
    1.8.0-hunter-p8
    URL
    "https://github.com/hunter-packages/googletest/archive/1.8.0-hunter-p8.tar.gz"
    SHA1
    1cdd396b20c8d29f7ea08baaa49673b1c261f545
)

hunter_add_version(
    PACKAGE_NAME
    GTest
    VERSION
    1.8.0-hunter-p9
    URL
    "https://github.com/hunter-packages/googletest/archive/1.8.0-hunter-p9.tar.gz"
    SHA1
    a345f16cb610e0b5dfa7778dc2852b784cfede5b
)

hunter_add_version(
    PACKAGE_NAME
    GTest
    VERSION
    1.8.0-hunter-p10
    URL
    "https://github.com/hunter-packages/googletest/archive/1.8.0-hunter-p10.tar.gz"
    SHA1
    1d92c9f51af756410843b13f8c4e4df09e235394
)

hunter_add_version(
    PACKAGE_NAME
    GTest
    VERSION
    "1.8.0-hunter-p11"
    URL
    "https://github.com/hunter-packages/googletest/archive/1.8.0-hunter-p11.tar.gz"
    SHA1
    76c6aec038f7d7258bf5c4f45c4817b34039d285
)

if(HUNTER_GTest_VERSION VERSION_LESS 1.8.0)
  set(_gtest_license "LICENSE")
else()
  set(_gtest_license "googletest/LICENSE")
endif()

hunter_cmake_args(
    GTest
    CMAKE_ARGS
    HUNTER_INSTALL_LICENSE_FILES=${_gtest_license}
)

hunter_pick_scheme(DEFAULT url_sha1_cmake)
hunter_cacheable(GTest)
hunter_download(PACKAGE_NAME GTest PACKAGE_INTERNAL_DEPS_ID 1)
$ mkdir cmake/Hunter
$ cat > cmake/Hunter/config.cmake <<EOF     # Добавление конфигурационного файла hunter
hunter_config(GTest VERSION 1.7.0-hunter-9)
EOF
```

Утилита, упрощающая работу с cmake

```ShellSession
$ git submodule add https://github.com/ruslo/polly tools/polly      # Добавим подмодуль
Cloning into '/Dimontich/workspace/projects/lab07/tools/polly'...
remote: Enumerating objects: 42, done.
remote: Counting objects: 100% (42/42), done.
remote: Compressing objects: 100% (26/26), done.
remote: Total 6147 (delta 23), reused 30 (delta 16), pack-reused 6105
Receiving objects: 100% (6147/6147), 1.57 MiB | 2.52 MiB/s, done.
Resolving deltas: 100% (4204/4204), done.
$ tools/polly/bin/polly.py --test           # Сборка и проверка тестов
Python version: 3.5
Build dir: /Dimontich/workspace/projects/lab07/_builds/default
Execute command: [
  `which`
  `cmake`
]

[/Dimontich/workspace/projects/lab07]> "which" "cmake"

/usr/bin/cmake
Execute command: [
  `cmake`
  `--version`
]

[/Dimontich/workspace/projects/lab07]> "cmake" "--version"

cmake version 3.7.2

CMake suite maintained and supported by Kitware (kitware.com/cmake).
Execute command: [
  `cmake`
  `-H.`
  `-B/Dimontich/workspace/projects/lab07/_builds/default`
  `-DCMAKE_TOOLCHAIN_FILE=/Dimontich/workspace/projects/lab07/tools/polly/default.cmake`
]

[/Dimontich/workspace/projects/lab07]> "cmake" "-H." "-B/Dimontich/workspace/projects/lab07/_builds/default" "-DCMAKE_TOOLCHAIN_FILE=/Dimontich/workspace/projects/lab07/tools/polly/default.cmake"

-- [polly] Used toolchain: Default
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
-- [hunter] Calculating Toolchain-SHA1
-- [hunter] Calculating Config-SHA1
-- [hunter] HUNTER_ROOT: /root/projects/hunter
-- [hunter] [ Hunter-ID: 12dec07 | Toolchain-ID: dffde48 | Config-ID: cedd094 ]
-- [hunter] GTEST_ROOT: /root/projects/hunter/_Base/12dec07/dffde48/cedd094/Install (ver.: 1.8.0-hunter-p11)
-- Configuring done
-- Generating done
-- Build files have been written to: /Dimontich/workspace/projects/lab07/_builds/default
Execute command: [
  `cmake`
  `--build`
  `/Dimontich/workspace/projects/lab07/_builds/default`
  `--`
]

[/Dimontich/workspace/projects/lab07]> "cmake" "--build" "/Dimontich/workspace/projects/lab07/_builds/default" "--"

Scanning dependencies of target print
[ 50%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
[100%] Linking CXX static library libprint.a
[100%] Built target print
Run tests
Execute command: [
  `ctest`
]

[/Dimontich/workspace/projects/lab07/_builds/default]> "ctest"

*********************************
No test configuration file found!
*********************************
Usage

  ctest [options]

-
Log saved: /Dimontich/workspace/projects/lab07/_logs/polly/default/log.txt
-
Generate: 0:00:03.471101s
Build: 0:00:01.294065s
Test: 0:00:00.013595s
-
Total: 0:00:04.779135s
-
SUCCESS
```

## Report

```ShellSession
$ popd
$ export LAB_NUMBER=07
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gistup -m "lab${LAB_NUMBER}"
```

## Homework

### Задание
1. Создайте cвой hunter-пакет.

## Links

- [Create Hunter package](https://docs.hunter.sh/en/latest/creating-new/create.html)
- [Custom Hunter config](https://github.com/ruslo/hunter/wiki/example.custom.config.id)
- [Polly](https://github.com/ruslo/polly)

```
Copyright (c) 2015-2019 The ISC Authors
```
