
# Лабораторная работа 5

# lab05

```sh                                                                                                                                                           ❯ export GITHUB_USERNAME=TheKamenski
                                                                                                     
❯ cd ${GITHUB_USERNAME}/workspace
                                                                             
❯ pushd .
~/TheKamenski/workspace ~

❯ source scripts/activate
```

```sh
❯ git clone git@github.com:${GITHUB_USERNAME}/lab04.git projects/lab05
Клонирование в «projects/lab05»...
remote: Enumerating objects: 34, done.
remote: Counting objects: 100% (34/34), done.
remote: Compressing objects: 100% (24/24), done.
remote: Total 34 (delta 8), reused 27 (delta 4), pack-reused 0
Получение объектов: 100% (34/34), 11.37 КиБ | 5.68 МиБ/с, готово.
Определение изменений: 100% (8/8), готово.
```

```sh
❯ cd projects/lab05
                                                
❯ git remote remove origin
                                             
❯ git remote add origin git@github.com:${GITHUB_USERNAME}/lab05.git
```

```sh
❯ mkdir third-party
                              
❯ git submodule add https://github.com/google/googletest.git third-party/gtest 
Клонирование в «/home/TheKamenski/workspace/projects/lab05/third-party/gtest»...
remote: Enumerating objects: 27501, done.
remote: Counting objects: 100% (39/39), done.
remote: Compressing objects: 100% (24/24), done.
remote: Total 27501 (delta 16), reused 29 (delta 10), pack-reused 27462
Получение объектов: 100% (27501/27501), 12.72 МиБ | 5.83 МиБ/с, готово.
Определение изменений: 100% (20429/20429), готово.

    ~/TheKamenski/workspace/projects/lab05    main +2                                              5s  
❯ cd third-party/gtest && git checkout v1.14.0 && cd ../..
Примечание: переключение на «v1.14.0».

Вы сейчас в состоянии «отсоединённого указателя HEAD». Можете осмотреться,
внести экспериментальные изменения и зафиксировать их, также можете
отменить любые коммиты, созданные в этом состоянии, не затрагивая другие
ветки, переключившись обратно на любую ветку.

Если хотите создать новую ветку для сохранения созданных коммитов, можете
сделать это (сейчас или позже), используя команду switch с параметром -c.
Например:

  git switch -c <новая-ветка>

Или отмените эту операцию с помощью:

  git switch -

Отключите этот совет, установив переменную конфигурации
advice.detachedHead в значение false

HEAD сейчас на f8d7d77c Bump version to v1.14 in preparation for release
```

```sh                                            
❯ git add third-party/gtest  
                                      
❯ git commit -m "added gtest framework"
[main f11c11d] added gtest framework
 2 files changed, 4 insertions(+)
 create mode 100644 .gitmodules
 create mode 160000 third-party/gtest
```

```sh
❯ sed -i '/option(BUILD_EXAMPLES "Build examples" OFF)/a\
option(BUILD_TESTS "Build tests" OFF)
' CMakeLists.txt
                                                 
❯ cat >> CMakeLists.txt <<EOF

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
❯ mkdir tests
                                              
❯ cat > tests/test1.cpp <<EOF
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
❯ cmake -H. -B_build -DBUILD_TESTS=ON 
CMake Deprecation Warning at CMakeLists.txt:1 (cmake_minimum_required):
  Compatibility with CMake < 3.5 will be removed from a future version of
  CMake.

  Update the VERSION argument <min> value or use a ...<max> suffix to tell
  CMake that the project does not need compatibility with older versions.


-- The C compiler identification is GNU 14.0.1
-- The CXX compiler identification is GNU 14.0.1
-- Detecting C compiler ABI info
-- Detecting C compiler ABI info - done
-- Check for working C compiler: /usr/bin/cc - skipped
-- Detecting C compile features
-- Detecting C compile features - done
-- Detecting CXX compiler ABI info
-- Detecting CXX compiler ABI info - done
-- Check for working CXX compiler: /usr/bin/c++ - skipped
-- Detecting CXX compile features
-- Detecting CXX compile features - done
-- Found Python3: /usr/bin/python3.12 (found version "3.12.2") found components: Interpreter 
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD
-- Performing Test CMAKE_HAVE_LIBC_PTHREAD - Success
-- Found Threads: TRUE  
-- Configuring done (2.2s)
-- Generating done (0.0s)
-- Build files have been written to: /home/TheKamenski/TheKamenski/workspace/projects/lab05/_build
```

```sh                                                
❯ cmake --build _build 
[  8%] Building CXX object CMakeFiles/print.dir/sources/print.cpp.o
[ 16%] Linking CXX static library libprint.a
[ 16%] Built target print
[ 25%] Building CXX object third-party/gtest/googletest/CMakeFiles/gtest.dir/src/gtest-all.cc.o
[ 33%] Linking CXX static library ../../../lib/libgtest.a
[ 33%] Built target gtest
[ 41%] Building CXX object third-party/gtest/googletest/CMakeFiles/gtest_main.dir/src/gtest_main.cc.o
[ 50%] Linking CXX static library ../../../lib/libgtest_main.a
[ 50%] Built target gtest_main
[ 58%] Building CXX object CMakeFiles/check.dir/tests/test1.cpp.o
[ 66%] Linking CXX executable check
[ 66%] Built target check
[ 75%] Building CXX object third-party/gtest/googlemock/CMakeFiles/gmock.dir/src/gmock-all.cc.o
[ 83%] Linking CXX static library ../../../lib/libgmock.a
[ 83%] Built target gmock
[ 91%] Building CXX object third-party/gtest/googlemock/CMakeFiles/gmock_main.dir/src/gmock_main.cc.o
[100%] Linking CXX static library ../../../lib/libgmock_main.a
[100%] Built target gmock_main
```

```sh
❯ cmake --build _build --target test 
Running tests...
Test project /home/TheKamenski/TheKamenski/workspace/projects/lab05/_build
    Start 1: check
1/1 Test #1: check ............................   Passed    0.01 sec

100% tests passed, 0 tests failed out of 1

Total Test time (real) =   0.01 sec
```

```sh                                    
❯ _build/check
Running main() from /home/TheKamenski/TheKamenski/workspace/projects/lab05/third-party/gtest/googletest/src/gtest_main.cc
[==========] Running 1 test from 1 test suite.
[----------] Global test environment set-up.
[----------] 1 test from Print
[ RUN      ] Print.InFileStream
[       OK ] Print.InFileStream (0 ms)
[----------] 1 test from Print (0 ms total)

[----------] Global test environment tear-down
[==========] 1 test from 1 test suite ran. (0 ms total)
[  PASSED  ] 1 test.
```

```sh                                         
❯ cmake --build _build --target test -- ARGS=--verbose
Running tests...
UpdateCTestConfiguration  from :/home/TheKamenski/TheKamenski/workspace/projects/lab05/_build/DartConfiguration.tcl
UpdateCTestConfiguration  from :/home/TheKamenski/TheKamenski/workspace/projects/lab05/_build/DartConfiguration.tcl
Test project /home/TheKamenski/TheKamenski/workspace/projects/lab05/_build
Constructing a list of tests
Done constructing a list of tests
Updating test list for fixtures
Added 0 tests to meet fixture requirements
Checking test dependency graph...
Checking test dependency graph end
test 1
    Start 1: check

1: Test command: /home/TheKamenski/TheKamenski/workspace/projects/lab05/_build/check
1: Working Directory: /home/TheKamenski/TheKamenski/workspace/projects/lab05/_build
1: Test timeout computed to be: 10000000
1: Running main() from /home/TheKamenski/TheKamenski/workspace/projects/lab05/third-party/gtest/googletest/src/gtest_main.cc
1: [==========] Running 1 test from 1 test suite.
1: [----------] Global test environment set-up.
1: [----------] 1 test from Print
1: [ RUN      ] Print.InFileStream
1: [       OK ] Print.InFileStream (0 ms)
1: [----------] 1 test from Print (0 ms total)
1: 
1: [----------] Global test environment tear-down
1: [==========] 1 test from 1 test suite ran. (0 ms total)
1: [  PASSED  ] 1 test.
1/1 Test #1: check ............................   Passed    0.01 sec

100% tests passed, 0 tests failed out of 1

Total Test time (real) =   0.01 sec
```

```sh                                              
❯ sed -i 's/lab04/lab05/g' README.md
                                        
❯ sed -i 's/\(DCMAKE_INSTALL_PREFIX=_install\)/\1 -DBUILD_TESTS=ON/' .travis.yml 
                                              
❯ sed -i '/cmake --build _build --target install/a\
- cmake --build _build --target test -- ARGS=--verbose
' .travis.yml
```

```sh                                            
❯ git add .travis.yml
                                          
❯ git add tests
```

```sh
❯ git add -p
diff --git a/CMakeLists.txt b/CMakeLists.txt
index 96a361e..aa7a323 100644
--- a/CMakeLists.txt
+++ b/CMakeLists.txt
@@ -4,6 +4,7 @@ set(CMAKE_CXX_STANDARD 11)
 set(CMAKE_CXX_STANDARD_REQUIRED ON)
 
 option(BUILD_EXAMPLES "Build examples" OFF)
+option(BUILD_TESTS "Build tests" OFF)
 
 project(print)
 
(1/2) Индексировать этот блок [y,n,q,a,d,j,J,g,/,e,?]? y
@@ -34,3 +35,12 @@ install(TARGETS print
 
 install(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/include/ DESTINATION include)
 install(EXPORT print-config DESTINATION cmake)
+
+if(BUILD_TESTS)
+  enable_testing()
+  add_subdirectory(third-party/gtest)
+  file(GLOB ${PROJECT_NAME}_TEST_SOURCES tests/*.cpp)
+  add_executable(check ${${PROJECT_NAME}_TEST_SOURCES})
+  target_link_libraries(check ${PROJECT_NAME} gtest_main)
+  add_test(NAME check COMMAND check)
+endif()
(2/2) Индексировать этот блок [y,n,q,a,d,K,g,/,e,?]? y

diff --git a/README.md b/README.md
index 442ec40..6516588 100644
--- a/README.md
+++ b/README.md
@@ -1,8 +1,8 @@
-[![Build Status](https://app.travis-ci.com/TheKamenski/lab04.svg?token=QmQqzGNVkZy8A7N9cEfZ&branch=master)](https://app.travis-ci.com/TheKamenski/lab04)
+[![Build Status](https://app.travis-ci.com/TheKamenski/lab05.svg?token=QmQqzGNVkZy8A7N9cEfZ&branch=master)](https://app.travis-ci.com/TheKamenski/lab05)
 
 # Лабораторная работа 4. ИУ8-24 Бачулашвили София
 
-# lab04
+# lab05
 
                                                                                                                                 
 ❯ export GITHUB_USERNAME=TheKamenski 
(1/4) Индексировать этот блок [y,n,q,a,d,j,J,g,/,s,e,?]? y
@@ -18,8 +18,8 @@

-git clone git@github.com:${GITHUB_USERNAME}/lab03.git projects/lab04
-Клонирование в «projects/lab04»...
+git clone git@github.com:${GITHUB_USERNAME}/lab03.git projects/lab05
+Клонирование в «projects/lab05»...
 remote: Enumerating objects: 25, done.
 remote: Counting objects: 100% (25/25), done.
 remote: Compressing objects: 100% (16/16), done.
(2/4) Индексировать этот блок [y,n,q,a,d,K,j,J,g,/,e,?]? y
@@ -29,10 +29,10 @@ remote: Total 25 (delta 3), reused 25 (delta 3), pack-reused 0


-❯ cd projects/lab04
+❯ cd projects/lab05
  git remote remove origin
                                                                                      
-❯ git remote add origin git@github.com:${GITHUB_USERNAME}/lab04.git
+❯ git remote add origin git@github.com:${GITHUB_USERNAME}/lab05.git


(3/4) Индексировать этот блок [y,n,q,a,d,K,j,J,g,/,s,e,?]? y
@@ -74,6 +74,6 @@ EOF
 Запись объектов: 100% (28/28), 8.71 КиБ | 4.35 МиБ/с, готово.
 Total 28 (delta 4), reused 24 (delta 3), pack-reused 0 (from 0)
 remote: Resolving deltas: 100% (4/4), done.
-To github.com:TheKamenski/lab04.git
+To github.com:TheKamenski/lab05.git
  * [new branch]      main -> main
 ```
(4/4) Индексировать этот блок [y,n,q,a,d,K,g,/,e,?]? y


```sh
❯ git commit -m "added tests"
[main 4ad0762] added tests
 4 files changed, 38 insertions(+), 8 deletions(-)
 create mode 100644 tests/test1.cpp
                                               
❯ git push origin main
Перечисление объектов: 45, готово.
Подсчет объектов: 100% (45/45), готово.
При сжатии изменений используется до 12 потоков
Сжатие объектов: 100% (29/29), готово.
Запись объектов: 100% (45/45), 13.44 КиБ | 4.48 МиБ/с, готово.
Total 45 (delta 14), reused 31 (delta 8), pack-reused 0 (from 0)
remote: Resolving deltas: 100% (14/14), done.
To github.com:TheKamenski/lab05.git
 * [new branch]      main -> main
```
