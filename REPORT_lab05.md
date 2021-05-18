[![Build Status](https://travis-ci.com/Sudar-Kudr/lab05.svg?branch=main)](https://travis-ci.com/Sudar-Kudr/lab05)
## Laboratory work V

Данная лабораторная работа посвещена изучению фреймворков для тестирования на примере **GTest**

```sh
$ open https://github.com/google/googletest
```

## Tasks

- [ok] 1. Создать публичный репозиторий с названием **lab05** на сервисе **GitHub**
- [ok] 2. Выполнить инструкцию учебного материала
- [ok] 3. Ознакомиться со ссылками учебного материала
- [ok] 4. Составить отчет и отправить ссылку личным сообщением в **Slack**

## Tutorial

```sh
$ export GITHUB_USERNAME=<имя_пользователя>     #присваиваем <имя_пользователя> в переменную GITHUB_USERNAME
$ alias gsed=sed # for *-nix system            #заменяем команду sed на gsed
```

```sh
$ cd ${GITHUB_USERNAME}/workspace     #спускаемся в workspace
$ pushd .                            #добавляем в стек текущий каталог
$ source scripts/activate           #выполняем скрипт
```

```sh
$ git clone https://github.com/${GITHUB_USERNAME}/lab04 projects/lab05   #клонируем репозиторий из lab04 в директорию projects/lab05
$ cd projects/lab05                                                     #переходим директорию projects/lab05
$ git remote remove origin                                             #удаляем старую ссылку репозитория
$ git remote add origin https://github.com/${GITHUB_USERNAME}/lab05   #добавляем ссылку репозитория в управление репозиториями
```

```sh
$ mkdir third-party                                                          #создаем папку third-party
$ git submodule add https://github.com/google/googletest third-party/gtest     #Создаем подмодуль gtest, клонируем в него библиотеку Gtest
$ cd third-party/gtest && git checkout release-1.8.1 && cd ../..             #спускаемся в папку third-party/gtest, создаем ветку release-1.8.1 в нашем подмодуле, и возвращаемся в обратно
$ git add third-party/gtest                                                    #добавляем third-party/gtest
$ git commit -m "added gtest framework"                                      #закомментируем его
```

```sh
$ gsed -i '/option(BUILD_EXAMPLES "Build examples" OFF)/a\  #добавляем в CMakeLists.txt переменную, отвечающую за сборку тестов
option(BUILD_TESTS "Build tests" OFF)
' CMakeLists.txt
$ cat >> CMakeLists.txt <<EOF                  #открываем файл и пишем данные от EOF до EOF

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

```sh
$ mkdir tests                     #создаем директорию tests
$ cat > tests/test1.cpp <<EOF      #создаем файл test1.cpp и пишем код от EOF до EOF
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

```sh
$ cmake -H. -B_build -DBUILD_TESTS=ON        #меняем значение переменной BUILD TESTS на ON
$ cmake --build _build                      #запускаем сборку
$ cmake --build _build --target test         #запускаем сборку test
```

```sh
$ _build/check                                           #проверяем тесты с помощью Gtest
$ cmake --build _build --target test -- ARGS=--verbose     #запускаем сборку test
```

```sh
$ gsed -i 's/lab04/lab05/g' README.md                                               #поменяем в README.md все строки lab04 на lab05
$ gsed -i 's/\(DCMAKE_INSTALL_PREFIX=_install\)/\1 -DBUILD_TESTS=ON/' .travis.yml     #добавляем в .travis.yml строку
$ gsed -i '/cmake --build _build --target install/a\                                #добавляем в .travis.yml команду
- cmake --build _build --target test -- ARGS=--verbose
' .travis.yml
```

```sh
$ travis lint                #проверяем .travis.yml на любые проблемы, которые он может обнаружить
```

```sh
$ git add .travis.yml        #добавляем .travis.yml
$ git add tests               #добавляем tests
$ git add -p                   #посмотрим что изменилось
$ git commit -m "added tests"   #закомментируем их
$ git push origin main           #отправляем данные на сервер, в удаленный репозиторий main
```

```sh
$ travis login --auto         #авторизируемся на travis
$ travis enable             #делаем проект доступным
```

```sh
$ mkdir artifacts                                                     #создаем директорию artifacts
$ sleep 20s && gnome-screenshot --file artifacts/screenshot.png      #делаем скриншот экрана переместим его в директорию artifacts
# for macOS: $ screencapture -T 20 artifacts/screenshot.png         
# open https://github.com/${GITHUB_USERNAME}/lab05                 
```

## Report

```sh
$ popd                                                                           #удаляем из стека текущий каталог
$ export LAB_NUMBER=05                                                          #присваиваем 05 в переменную LAB_NUMBER
$ git clone https://github.com/tp-labs/lab${LAB_NUMBER} tasks/lab${LAB_NUMBER} #клонируем из ссылки в директорию (в наше случае-tasks/lab05)
$ mkdir reports/lab${LAB_NUMBER}                                              #создаем в директории reports папку (в нашем случае- lab05)                                      
$ cp tasks/lab${LAB_NUMBER}/README.md reports/lab${LAB_NUMBER}/REPORT.md     #спускаемся в директорию (в наше случае- lab05)
$ cd reports/lab${LAB_NUMBER}                                               #копируем из одной директории в другую
$ edit REPORT.md                                                           #редактируем REPORT.md
$ gist REPORT.md                                                          #сохраняем REPORT.md
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
Copyright (c) 2015-2021 The ISC Authors
```
