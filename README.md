[![Build Status](https://travis-ci.org/Dimontich/lab03.svg?branch=master)](https://travis-ci.org/Dimontich/lab03)
## Laboratory work III

Данная лабораторная работа посвещена изучению систем автоматизации сборки проекта на примере **CMake**

```ShellSession
$ open https://cmake.org/
```

## Tasks

- [x] 1. Создать публичный репозиторий с названием **lab03** на сервисе **GitHub**
- [x] 2. Ознакомиться со ссылками учебного материала
- [x] 3. Выполнить инструкцию учебного материала
- [x] 4. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Tutorial

Установка переменной

```ShellSession
$ export GITHUB_USERNAME=Dimontich   # Установка переменной GITHUB_USERNAME
```

Подготовка

```ShellSession
$ cd ${GITHUB_USERNAME}/workspace   # Переход в директорию workspace
$ pushd .         # Сохранение директории 
/Dimontich/workspace /Dimontich/workspace
$ source scripts/activate       # Выполнение скрипта 
```

Получение необходимых файлов

```ShellSession
$ git clone https://github.com/${GITHUB_USERNAME}/lab02.git projects/lab03   # Клонирование репо
Cloning into 'projects/lab03'...
remote: Enumerating objects: 27, done.
remote: Counting objects: 100% (27/27), done.
remote: Compressing objects: 100% (25/25), done.
remote: Total 27 (delta 7), reused 3 (delta 0), pack-reused 0
Unpacking objects: 100% (27/27), done.
$ cd projects/lab03    # Переход в директорию
$ git remote remove origin # Удаление ссылки на репозиторий
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab03.git   # Добавление ссылки на репозиторий
```

Сборка через g++, ar

```ShellSession
# Компиляция (Стандарт c++11, заголовочные файлы препроцессор тянет из директории ./include)
$ g++ -std=c++11 -I./include -c sources/print.cpp # (компилируем sources/print.cpp)
$ ls print.o        # Проверим, создался ли файл
print.o
$ nm print.o | grep print           # Получение сигнатур функций, содержащих print
0000000000000095 t _GLOBAL__sub_I__Z5printRKNSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEERSo
0000000000000000 T _Z5printRKNSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEERSo
0000000000000026 T _Z5printRKNSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEERSt14basic_ofstreamIcS2_E
$ ar rvs print.a print.o # Добавим файл print.o в статическую библиотеку (архив без сжатия) print.a
ar: creating print.a
a - print.o
$ file print.a      # Информация о файле print.a
print.a: current ar archive
$ g++ -std=c++11 -I./include -c examples/example1.cpp   # Аналогично
$ ls example1.o   # Проверим, создался ли файл
example1.o
$ g++ example1.o print.a -o example1    # Линковка example1.o и print.a в example1
$ ./example1 && echo                # Исполнение исполняемого файла
hello
```

Сборка через g++, ar

```ShellSession
$ g++ -std=c++11 -I./include -c examples/example2.cpp # Компиляция (аналогично)
$ nm example2.o         # Посмотрим сигнатуры функций в этой файле
0000000000000000 V DW.ref.__gxx_personality_v0
                 U _GLOBAL_OFFSET_TABLE_
0000000000000134 t _GLOBAL__sub_I_main
                 U _Unwind_Resume
00000000000000eb t _Z41__static_initialization_and_destruction_0ii
                 U _Z5printRKNSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEERSt14basic_ofstreamIcS2_E
                 U _ZNSaIcEC1Ev
                 U _ZNSaIcED1Ev
                 U _ZNSt14basic_ofstreamIcSt11char_traitsIcEEC1EPKcSt13_Ios_Openmode
                 U _ZNSt14basic_ofstreamIcSt11char_traitsIcEED1Ev
                 U _ZNSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEEC1EPKcRKS3_
                 U _ZNSt7__cxx1112basic_stringIcSt11char_traitsIcESaIcEED1Ev
                 U _ZNSt8ios_base4InitC1Ev
                 U _ZNSt8ios_base4InitD1Ev
0000000000000000 r _ZStL19piecewise_construct
0000000000000000 b _ZStL8__ioinit
0000000000000000 W _ZStorSt13_Ios_OpenmodeS_
                 U __cxa_atexit
                 U __dso_handle
                 U __gxx_personality_v0
0000000000000000 T main
$ g++ example2.o print.a -o example2            # Компиляция (аналогично)
$ ./example2                            # Исполнение
$ cat log.txt && echo     # Вывод в консоль log.txt
hello
```

Удаление лишних файлов

```ShellSession
$ rm -rf example1.o example2.o print.o
$ rm -rf print.a
$ rm -rf example1 example2
$ rm -rf log.txt
```

Создание CMakeLists.txt (описали минимальную версию и название проекта)

```ShellSession
$ cat > CMakeLists.txt <<EOF
cmake_minimum_required(VERSION 3.4)
project(print)
EOF
```

Описали стандарт C++ (и указали, что выполнение обязательно)

```ShellSession
$ cat >> CMakeLists.txt <<EOF
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
EOF
```

Описали цель: статическая библиотека print (в нее поместится скомпилированный указанный cpp файл)

```ShellSession
$ cat >> CMakeLists.txt <<EOF
add_library(print STATIC \${CMAKE_CURRENT_SOURCE_DIR}/sources/print.cpp)
EOF
```

Указание препроцессору на директорию, из которой надо тащить заголовочные файлы

```ShellSession
$ cat >> CMakeLists.txt <<EOF
include_directories(\${CMAKE_CURRENT_SOURCE_DIR}/include)
EOF
```

Сборка через cmake

```ShellSession
$ cmake -H. -B_build        # Конфигурирование
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
-- Build files have been written to: /Dimontich/workspace/projects/lab03/_build
$ cmake --build _build          # Сборка
Scanning dependencies of target print
[ 50%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
[100%] Linking CXX static library libprint.a
[100%] Built target print
```

Описание цели: исполняемый example1 и example2 с соответствующими cpp файлами

```ShellSession
$ cat >> CMakeLists.txt <<EOF

add_executable(example1 \${CMAKE_CURRENT_SOURCE_DIR}/examples/example1.cpp)
add_executable(example2 \${CMAKE_CURRENT_SOURCE_DIR}/examples/example2.cpp)
EOF
```

```ShellSession
$ cat >> CMakeLists.txt <<EOF

target_link_libraries(example1 print)
target_link_libraries(example2 print)
EOF
```

```ShellSession
$ cmake --build _build          # Конфигурирование
-- Configuring done
-- Generating done
-- Build files have been written to: /Dimontich/workspace/projects/lab03/_build
[ 33%] Built target print
Scanning dependencies of target example1
[ 50%] Building CXX object CMakeFiles/example1.dir/examples/example1.cpp.o
[ 66%] Linking CXX executable example1
[ 66%] Built target example1
Scanning dependencies of target example2
[ 83%] Building CXX object CMakeFiles/example2.dir/examples/example2.cpp.o
[100%] Linking CXX executable example2
[100%] Built target example2
$ cmake --build _build --target print           # Сборка цели print
[100%] Built target print
$ cmake --build _build --target example1   # Сборка цели example1
[ 50%] Built target print
[100%] Built target example1
$ cmake --build _build --target example2   # Сборка цели example1
[ 50%] Built target print
[100%] Built target example2
```

```ShellSession
$ ls -la _build/libprint.a          # Проверим, существует ли файл
-rw-r--r-- 1 root root 3134 Jun  9 21:05 _build/libprint.a
$ _build/example1 && echo           # Выполним исполняемый файл example1
hello
$ _build/example2    # Выполним исполняемый файл example2
$ cat log.txt && echo    # Вывод файла в консоль
hello
$ rm -rf log.txt         # Удалить
```

Вытянуть CMakeLists.txt из https://github.com/tp-labs/lab03

```ShellSession
$ git clone https://github.com/tp-labs/lab03 tmp
Cloning into 'tmp'...
remote: Enumerating objects: 73, done.
remote: Total 73 (delta 0), reused 0 (delta 0), pack-reused 73
Unpacking objects: 100% (73/73), done.
$ mv -f tmp/CMakeLists.txt .
$ rm -rf tmp
```



```ShellSession
$ cat CMakeLists.txt
cmake_minimum_required(VERSION 3.4)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

option(BUILD_EXAMPLES "Build examples" OFF)

project(print)

add_library(print STATIC ${CMAKE_CURRENT_SOURCE_DIR}/sources/print.cpp)

target_include_directories(print PUBLIC
  $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>
  $<INSTALL_INTERFACE:include>
)

if(BUILD_EXAMPLES)
  file(GLOB EXAMPLE_SOURCES "${CMAKE_CURRENT_SOURCE_DIR}/examples/*.cpp")
  foreach(EXAMPLE_SOURCE ${EXAMPLE_SOURCES})
    get_filename_component(EXAMPLE_NAME ${EXAMPLE_SOURCE} NAME_WE)
    add_executable(${EXAMPLE_NAME} ${EXAMPLE_SOURCE})
    target_link_libraries(${EXAMPLE_NAME} print)
    install(TARGETS ${EXAMPLE_NAME}
      RUNTIME DESTINATION bin
    )
  endforeach(EXAMPLE_SOURCE ${EXAMPLE_SOURCES})
endif()

install(TARGETS print
    EXPORT print-config
    ARCHIVE DESTINATION lib
    LIBRARY DESTINATION lib
)

install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/include/ DESTINATION include)
install(EXPORT print-config DESTINATION cmake)
$ cmake -H. -B_build -DCMAKE_INSTALL_PREFIX=_install        # Конфигурирование
-- Configuring done
-- Generating done
-- Build files have been written to: /Dimontich/workspace/projects/lab03/_build
$ cmake --build _build --target install # Установка
[100%] Built target print
Install the project...
-- Install configuration: ""
-- Installing: /Dimontich/workspace/projects/lab03/_install/lib/libprint.a
-- Installing: /Dimontich/workspace/projects/lab03/_install/include
-- Installing: /Dimontich/workspace/projects/lab03/_install/include/print.hpp
-- Installing: /Dimontich/workspace/projects/lab03/_install/cmake/print-config.cmake
-- Installing: /Dimontich/workspace/projects/lab03/_install/cmake/print-config-noconfig.cmake
$ tree _install   # Вывод дерева файлов
_install
|-- cmake
|   |-- print-config-noconfig.cmake
|   `-- print-config.cmake
|-- include
|   `-- print.hpp
`-- lib
    `-- libprint.a

3 directories, 4 files
```

Отправка изменений на удаленный репозиторий

```ShellSession
$ git add CMakeLists.txt            # Фиксация
$ git commit -m"added CMakeLists.txt"    # Коммит
[master 68b087e] added CMakeLists.txt
 1 file changed, 36 insertions(+)
 create mode 100644 CMakeLists.txt
$ git push origin master       # Отправка
Counting objects: 39, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (35/35), done.
Writing objects: 100% (39/39), 12.37 KiB | 0 bytes/s, done.
Total 39 (delta 9), reused 0 (delta 0)
remote: Resolving deltas: 100% (9/9), done.
To https://github.com/Dimontich/lab03.git
 * [new branch]      master -> master
```

## Report

```ShellSession
$ popd
$ export LAB_NUMBER=03
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gistup -m "lab${LAB_NUMBER}"
```

## Homework

Представьте, что вы стажер в компании "Formatter Inc.".
### Задание 1
Вам поручили перейти на систему автоматизированной сборки **CMake**.
Исходные файлы находятся в директории [formatter_lib](formatter_lib).
В этой директории находятся файлы для статической библиотеки *formatter*.
Создайте `CMakeList.txt` в директории [formatter_lib](formatter_lib),
с помощью которого можно будет собирать статическую библиотеку *formatter*.

### Задание 2
У компании "Formatter Inc." есть перспективная библиотека,
которая является расширением предыдущей библиотеки. Т.к. вы уже овладели
навыком созданием `CMakeList.txt` для статической библиотеки *formatter*, ваш 
руководитель поручает заняться созданием `CMakeList.txt` для библиотеки 
*formatter_ex*, которая в свою очередь использует библиотеку *formatter*.

### Задание 3
Конечно же ваша компания предоставляет примеры использования своих библиотек.
Чтобы продемонстрировать как работать с библиотекой *formatter_ex*,
вам необходимо создать два `CMakeList.txt` для двух простых приложений:
* *hello_world*, которое использует библиотеку *formatter_ex*;
* *solver*, приложение которое испольует статические библиотеки *formatter_ex* и *solver_lib*.

**Удачной стажировки!**

## Links
- [Основы сборки проектов на С/C++ при помощи CMake](https://eax.me/cmake/)
- [CMake Tutorial](http://neerc.ifmo.ru/wiki/index.php?title=CMake_Tutorial)
- [C++ Tutorial - make & CMake](https://www.bogotobogo.com/cplusplus/make.php)
- [Autotools](http://www.gnu.org/software/automake/manual/html_node/Autotools-Introduction.html)
- [CMake](https://cgold.readthedocs.io/en/latest/index.html)

```
Copyright (c) 2015-2019 The ISC Authors
```
