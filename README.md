## Laboratory work V

Данная лабораторная работа посвещена изучению фреймворков для тестирования на примере **GTest**

```sh
$ open https://github.com/google/googletest
```
## Homework

### Задание
1. Создайте `CMakeList.txt` для библиотеки *banking*.
2. Создайте модульные тесты на классы `Transaction` и `Account`.
    * Используйте mock-объекты.
    * Покрытие кода должно составлять 100%.
3. Настройте сборочную процедуру на **TravisCI**.
4. Настройте [Coveralls.io](https://coveralls.io/).
 ```sh
┌──(kali㉿kali)-[~]
└─$ /home/kali/GOSICK070404/workspace/projects/
                                                                                                                                                                             
┌──(kali㉿kali)-[~/GOSICK070404/workspace/projects]
└─$ git clone https://github.com/tp-labs/lab05 lab05
Cloning into 'lab05'...
remote: Enumerating objects: 137, done.
remote: Counting objects: 100% (25/25), done.
remote: Compressing objects: 100% (9/9), done.
remote: Total 137 (delta 18), reused 16 (delta 16), pack-reused 112
Receiving objects: 100% (137/137), 918.92 KiB | 2.01 MiB/s, done.
Resolving deltas: 100% (60/60), done.
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~/GOSICK070404/workspace/projects]
└─$ cd lab05               
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~/GOSICK070404/workspace/projects/lab05]
└─$ git init                                        
hint: Using 'master' as the name for the initial branch. This default branch name
hint: is subject to change. To configure the initial branch name to use in all
hint: of your new repositories, which will suppress this warning, call:
hint: 
hint:   git config --global init.defaultBranch <name>
hint: 
hint: Names commonly chosen instead of 'master' are 'main', 'trunk' and
hint: 'development'. The just-created branch can be renamed via this command:
hint: 
hint:   git branch -m <name>
Initialized empty Git repository in /home/kali/GOSICK070404/workspace/projects/lab05/.git/
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~/GOSICK070404/workspace/projects/lab05]
└─$ git remote remove origin
error: No such remote: 'origin'
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~/GOSICK070404/workspace/projects/lab05]
└─$ git remote add origin https://github.com/GOSICK070404/lab05
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~/GOSICK070404/workspace/projects/lab05]
└─$ git remote remove origin                                   
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~/GOSICK070404/workspace/projects/lab05]
└─$ git remote add origin https://github.com/GOSICK070404/lab05
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~/GOSICK070404/workspace/projects/lab05]
└─$  git submodule add  https://github.com/google/googletest.git
Cloning into '/home/kali/GOSICK070404/workspace/projects/lab05/googletest'...
remote: Enumerating objects: 26402, done.
remote: Counting objects: 100% (9781/9781), done.
remote: Compressing objects: 100% (548/548), done.
remote: Total 26402 (delta 9392), reused 9236 (delta 9233), pack-reused 16621
Receiving objects: 100% (26402/26402), 12.09 MiB | 3.19 MiB/s, done.
Resolving deltas: 100% (19622/19622), done.
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~/GOSICK070404/workspace/projects/lab05]
└─$  cd banking
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~/…/workspace/projects/lab05/banking]
└─$ rm CMakeList.txt
    ```                                                                                                                                                         ```sh                                                                              
┌──(kali㉿kali)-[~/…/workspace/projects/lab05/banking]
└─$ nano CMakeLists.txt
  ```
  ```sh
cmake_minimum_required(VERSION 3.4)
project(bank_lib)
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
add_library(banking STATIC Account.cpp Account.h Transaction.cpp Transaction.h)
   ```                                                                                                                                                        ```sh                                                                                 
┌──(kali㉿kali)-[~/…/workspace/projects/lab05/banking]
└─$ mkdir .github
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~/…/workspace/projects/lab05/banking]
└─$ mkdir .github/workflows
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~/…/workspace/projects/lab05/banking]
└─$ cd .github/workflows
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~/…/lab05/banking/.github/workflows]
└─$ nano cmake.yml
    ```
    ```sh
name: CMake
on:
 push:
  branches: [master]
 pull_request:
  branches: [master]
jobs:
 build_Linux:
  runs-on: ubuntu-latest
  steps:
  - uses: actions/checkout@v3
  - name: Adding gtest
    run: git clone https://github.com/google/googletest.git third-party/gtest -b release-1.11.0
  - name: Install lcov
    run: sudo apt-get install -y lcov
  - name: Config banking with tests
    run: cmake -H. -B ${{github.workspace}}/build -DBUILD_TESTS=ON
  - name: Build banking
    run: cmake --build ${{github.workspace}}/build
  - name: Run tests
    run: build/check
  - name: Do lcov stuff
    run: lcov -c -d build/CMakeFiles/banking.dir/banking/ --include *.cpp --output-file ./coverage/lcov.info
  - name: Publish to coveralls.io
    uses: coverallsapp/github-action@v1.1.2
    with:
      github-token: ${{ secrets.GITHUB_TOKEN }}
    ```
    ```sh
┌──(kali㉿kali)-[~/…/lab05/banking/.github/workflows]
└─$ nano CMakeLists.txt
    ```
    ```sh
      cmake_minimum_required(VERSION 3.4)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

option(BUILD_TESTS "Build tests" OFF)

if(BUILD_TESTS)
  add_compile_options(--coverage)
endif()

project (banking)

add_library(banking STATIC ${CMAKE_CURRENT_SOURCE_DIR}/banking/Transaction.cpp ${CMAKE_CURRENT_SOURCE_DIR}/banking/Account.cpp)
target_include_directories(banking PUBLIC
${CMAKE_CURRENT_SOURCE_DIR}/banking )

target_link_libraries(banking gcov)

if(BUILD_TESTS)
  enable_testing()
  add_subdirectory(third-party/gtest)
  file(GLOB BANKING_TEST_SOURCES tests/*.cpp)
  add_executable(check ${BANKING_TEST_SOURCES})
  target_link_libraries(check banking gtest_main gmock_main)
  add_test(NAME check COMMAND check)
endif()
  ```
  ```sh
┌──(kali㉿kali)-[~/…/lab05/banking/.github/workflows]
└─$  mkdir tests
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~/…/lab05/banking/.github/workflows]
└─$ cd tests
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~/…/banking/.github/workflows/tests]
└─$ nano test_Account.cpp
   ```
   ```sh
#include <Account.h>
#include <gtest/gtest.h>

TEST(Account, Banking){
	Account test(0,0);
	ASSERT_EQ(test.GetBalance(), 0);
	ASSERT_THROW(test.ChangeBalance(100), std::runtime_error);
	test.Lock();
	ASSERT_NO_THROW(test.ChangeBalance(100));
	ASSERT_EQ(test.GetBalance(), 100);
	ASSERT_THROW(test.Lock(), std::runtime_error);
	test.Unlock();
	ASSERT_THROW(test.ChangeBalance(100), std::runtime_error);
}
 ```
 ```sh
┌──(kali㉿kali)-[~/…/banking/.github/workflows/tests]
└─$ nano test_Transaction.cpp
  ```
  ```sh
#include <Account.h>
#include <Transaction.h>
#include <gtest/gtest.h>

TEST(Transaction, Banking){
	const int base_A = 5000, base_B = 5000, base_fee = 100;
	Account Alice(0,base_A), Bob(1,base_B);
	Transaction test_tran;
	ASSERT_EQ(test_tran.fee(), 1);
	test_tran.set_fee(base_fee);
	ASSERT_EQ(test_tran.fee(), base_fee);
	ASSERT_THROW(test_tran.Make(Alice, Alice, 1000), std::logic_error);
	ASSERT_THROW(test_tran.Make(Alice, Bob, -50), std::invalid_argument);
	ASSERT_THROW(test_tran.Make(Alice, Bob, 50), std::logic_error);
	if (test_tran.fee()*2-1 >= 100)
		ASSERT_EQ(test_tran.Make(Alice, Bob, test_tran.fee()*2-1), false);
	Alice.Lock();
	ASSERT_THROW(test_tran.Make(Alice, Bob, 1000), std::runtime_error);
	Alice.Unlock();
	ASSERT_EQ(test_tran.Make(Alice, Bob, 1000), true);
	ASSERT_EQ(Bob.GetBalance(), base_B+1000);
	ASSERT_EQ(Alice.GetBalance(), base_A-1000-base_fee);
	ASSERT_EQ(test_tran.Make(Alice, Bob, 3900), false);
	ASSERT_EQ(Bob.GetBalance(), base_B+1000);
	ASSERT_EQ(Alice.GetBalance(), base_A-1000-base_fee);
}
 ```
 ```sh
┌──(kali㉿kali)-[~/…/banking/.github/workflows/tests]
└─$  mkdir coverage
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~/…/banking/.github/workflows/tests]
└─$  cd coverage
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~/…/.github/workflows/tests/coverage]
└─$ touch temp.txt
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~/…/.github/workflows/tests/coverage]
└─$ git add -A
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~/…/.github/workflows/tests/coverage]
└─$ git commit -m"from tp-labs with tests"
[master (root-commit) 6483f8e] from tp-labs with tests
 15 files changed, 597 insertions(+)
 create mode 100644 .gitmodules
 create mode 100644 LICENSE
 create mode 100644 README.md
 create mode 100644 banking/.github/workflows/CMakeLists.txt
 create mode 100644 banking/.github/workflows/cmake.yml
 create mode 100644 banking/.github/workflows/tests/coverage/temp.txt
 create mode 100644 banking/.github/workflows/tests/test_Account.cpp
 create mode 100644 banking/.github/workflows/tests/test_Transaction.cpp
 create mode 100644 banking/Account.cpp
 create mode 100644 banking/Account.h
 create mode 100644 banking/CMakeLists.txt
 create mode 100644 banking/Transaction.cpp
 create mode 100644 banking/Transaction.h
 create mode 160000 googletest
 create mode 100644 preview.png
                                                                                                                                                                                                                                           
┌──(kali㉿kali)-[~/…/.github/workflows/tests/coverage]
└─$ git push origin master
Username for 'https://github.com': GOSICK070404
Password for 'https://GOSICK070404@github.com': 
Enumerating objects: 21, done.
Counting objects: 100% (21/21), done.
Delta compression using up to 2 threads
Compressing objects: 100% (18/18), done.
Writing objects: 100% (21/21), 889.13 KiB | 20.68 MiB/s, done.
Total 21 (delta 0), reused 0 (delta 0), pack-reused 0
To https://github.com/GOSICK070404/lab05
 * [new branch]      master -> master
 ```
## Links

- [C++ CI: Travis, CMake, GTest, Coveralls & Appveyor](http://david-grs.github.io/cpp-clang-travis-cmake-gtest-coveralls-appveyor/)
- [Boost.Tests](http://www.boost.org/doc/libs/1_63_0/libs/test/doc/html/)
- [Catch](https://github.com/catchorg/Catch2)

```
Copyright (c) 2015-2021 The ISC Authors
```
