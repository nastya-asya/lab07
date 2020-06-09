## Laboratory work VII

[![Build Status](https://travis-ci.com/nastya-asya/lab07.svg?branch=master)](https://travis-ci.com/nastya-asya/lab07)

<a href="https://yandex.ru/efir/?stream_id=vDHLoKtKoa3o"><img src="https://raw.githubusercontent.com/tp-labs/lab07/master/preview.png" width="640"/></a>

Данная лабораторная работа посвещена изучению систем управления пакетами на примере **Hunter**

```sh
$ open https://github.com/ruslo/hunter
```

## Tasks

- [x] 1. Создать публичный репозиторий с названием **lab07** на сервисе **GitHub**
- [x] 2. Выполнить инструкцию учебного материала
- [x] 3. Ознакомиться со ссылками учебного материала
- [x] 4. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Tutorial

```sh
$ export GITHUB_USERNAME=nastya-asya
$ alias gsed=sed
```

```sh
$ cd ${GITHUB_USERNAME}/workspace
$ pushd .
$ source scripts/activate
```

```sh
$ git clone https://github.com/${GITHUB_USERNAME}/lab06 projects/lab07
$ cd projects/lab07
$ git remote remove origin
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab07
```

```sh
$ mkdir -p cmake
$ wget https://raw.githubusercontent.com/cpp-pm/gate/master/cmake/HunterGate.cmake -O cmake/HunterGate.cmake
$ gsed -i '/cmake_minimum_required(VERSION 3.4)/a\

include("cmake/HunterGate.cmake")
HunterGate(
    URL "https://github.com/cpp-pm/hunter/archive/v0.23.251.tar.gz"
    SHA1 "5659b15dc0884d4b03dbd95710e6a1fa0fc3258d"
)
' CMakeLists.txt
```

```sh
$ git rm -rf third-party/gtest
$ gsed -i '/set(PRINT_VERSION_STRING "v\${PRINT_VERSION}")/a\

hunter_add_package(GTest)
find_package(GTest CONFIG REQUIRED)
' CMakeLists.txt
$ gsed -i 's/add_subdirectory(third-party/gtest)//' CMakeLists.txt
$ gsed -i 's/gtest_main/GTest::gtest_main/' CMakeLists.txt
```

```sh
$ cmake -H. -B_builds -DBUILD_TESTS=ON
$ cmake --build _builds
$ cmake --build _builds --target test
$ ls -la $HOME/.hunter
```

```sh
$ git clone https://github.com/cpp-pm/hunter $HOME/projects/hunter
$ export HUNTER_ROOT=$HOME/projects/hunter
$ rm -rf _builds
$ cmake -H. -B_builds -DBUILD_TESTS=ON
$ cmake --build _builds
$ cmake --build _builds --target test
```

```sh
$ cat $HUNTER_ROOT/cmake/configs/default.cmake | grep GTest
$ cat $HUNTER_ROOT/cmake/projects/GTest/hunter.cmake
$ mkdir cmake/Hunter
$ cat > cmake/Hunter/config.cmake <<EOF
hunter_config(GTest VERSION 1.7.0-hunter-9)
EOF
# add LOCAL in HunterGate function
```

```sh
$ mkdir demo
$ cat > demo/main.cpp <<EOF
#include <print.hpp>

#include <cstdlib>

int main(int argc, char* argv[])
{
  const char* log_path = std::getenv("LOG_PATH");
  if (log_path == nullptr)
  {
    std::cerr << "undefined environment variable: LOG_PATH" << std::endl;
    return 1;
  }
  std::string text;
  while (std::cin >> text)
  {
    std::ofstream out{log_path, std::ios_base::app};
    print(text, out);
    out << std::endl;
  }
}
EOF

$ gsed -i '/endif()/a\

add_executable(demo ${CMAKE_CURRENT_SOURCE_DIR}/demo/main.cpp)
target_link_libraries(demo print)
install(TARGETS demo RUNTIME DESTINATION bin)
' CMakeLists.txt
```

```sh
$ mkdir tools
$ git submodule add https://github.com/ruslo/polly tools/polly
$ tools/polly/bin/polly.py --test
$ tools/polly/bin/polly.py --install
$ tools/polly/bin/polly.py --toolchain clang-cxx14
```

## Report

```sh
$ popd
$ export LAB_NUMBER=07
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER}
$ mkdir reports/lab${LAB_NUMBER}
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md
$ cd reports/lab${LAB_NUMBER}
$ edit REPORT.md
$ gist REPORT.md
```

## Homework

[![Build Status](https://travis-ci.com/nastya-asya/hw07.svg?branch=master)](https://travis-ci.com/nastya-asya/hw07) [![Build Status](https://ci.appveyor.com/api/projects/status/wkbl6ku2g573k1v4?svg=true)](https://ci.appveyor.com/project/nastya-asya/hw07)

### Задание
1. Создайте cвой hunter-пакет.

Создадим настройки менеджера пакетов Hunter для репозитория с hw05. Настройка git-репозитория hw07 для работы

```sh
$ git clone https://github.com/${GITHUB_USERNAME}/hw05 hw07
$ cd hw07
$ git remote remove origin
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab07
```

Скачивание и подключение модуля HunterGate

```sh
$ mkdir -p cmake 
$ wget https://raw.githubusercontent.com/cpp-pm/gate/master/cmake/HunterGate.cmake -O cmake/HunterGate.cmake
$ sed -i "" '/cmake_minimum_required(VERSION 3.10)/a\
include("cmake/HunterGate.cmake")
HunterGate(
    URL "https:\//github.com/cpp-pm/hunter/archive/v0.23.251.tar.gz"
    SHA1 "5659b15dc0884d4b03dbd95710e6a1fa0fc3258d"
)
' CMakeLists.txt
```

Теперь не нужно скачивать GTest самостоятельно. Hunter сам подтянет добавленные с помощью функции hunter_add_package.

```sh
$ git rm -rf third-party/gtest
rm 'third-party/gtest'
$ sed -i "" '/option(BUILD_TESTS "Build tests" OFF)/a\
hunter_add_package(GTest)
find_package(GTest CONFIG REQUIRED)
' CMakeLists.txt
$ sed -i "" 's/add_subdirectory(third-party/gtest)//' CMakeLists.txt
$ sed -i "" 's/gtest_main/GTest::gtest_main/' CMakeLists.txt
$ cmake -H. -B_builds -DBUILD_TESTS=ON
$ cmake --build _builds
$ ls -la $HOME/.hunter
```

Добавление конфигурационного файла в проект, который будет содержать необходимую версию GTest.

```sh
$ mkdir cmake/Hunter
$ cat > cmake/Hunter/config.cmake <<EOF
hunter_config(GTest VERSION 1.7.0-hunter-9)
EOF
```

Добавление подмодуля polly, который содержит инструкции для сборки проектов с установленным Hunter.

```sh
$ mkdir tools
$ git submodule add https://github.com/ruslo/polly tools/polly
$ tools/polly/bin/polly.py --test
...
Generate: 0:00:03.640991s
Build: 0:00:01.585695s
Test: 0:00:00.013631s
-
Total: 0:00:05.240688s
-
SUCCESS
$ tools/polly/bin/polly.py --toolchain clang-cxx14
...
Generate: 0:00:03.759353s
Build: 0:00:01.613048s
-
Total: 0:00:05.372811s
-
SUCCESS
```

Создание appveyor.yml

```sh
$ cat >> appveyor.yml <<EOF
image: Visual Studio 2019
platform:
  - x86
  - x64
configuration: Release

build_script:
  - cmd: cmake -H. -B_build -DBUILD_TESTS=ON
  - cmd: cmake --build _build
  - cmd: cmake --build _build --target test
  - cmd: _build/check
  - cmd: cmake --build _build --target test -- ARGS=--verbose
EOF
```

## Links

- [Create Hunter package](https://docs.hunter.sh/en/latest/creating-new/create.html)
- [Custom Hunter config](https://github.com/ruslo/hunter/wiki/example.custom.config.id)
- [Polly](https://github.com/ruslo/polly)

```
Copyright (c) 2015-2020 The ISC Authors
```
