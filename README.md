# googletesteclipse
eclipse project for googletest

This repository contains an eclipse project to build googletest (gmock and gtest) and export the relevent include paths and libraries for use in other eclipse projects.

googletest itself is referenced through a git submodule. To clone this project add --recursive to your git clone command.

The intention is to add different build configurations as I use googletest on different platforms, for now the only build target is for Linux gcc.

## Usage
To use this googletest project with unit tests in another project, both projects must exist in the same workspace in eclipse. This example shows how to create a new test project f you have an exisiting project then just add a refernce..

Having cloned this repository, the eclipe select *File->Import...->General->Existing Projects into Workspace*, browse to the root of the cloned repository, select the googletest project and click finish.

Create a new empty C/C++ executable project in eclipse, using GCC linux. In the project add a source file with this content

~~~~
  #include "gtest/gtest.h"

  TEST(TestCaseName, TestName)
  {
      FAIL();
  };
~~~~

Then in your test project select *Properties->C/C++ General->Paths and Symbols* fine the *References* tab at the extreme right hand end of the tabs, and tick googletest. This Makes your project depend on googletest, and also imports the include paths and library settings required to use the library. This should be sufficient for you to be able to build your tests.

I also add *makefile.target* to my project, with content like this (Indentation MUST use tabs), where *test* is the name of your project.
~~~~
  runtests : test
      ./test

  .PHONY: test
~~~~

Then in your test project select *Properties->C/C++ Build*, and on the behaviour tab change the setting for *Build (incremental build)* to *runtests*, now when you build the tests will also run.
~~~~
20:41:16 **** Build of configuration Debug for project test ****
make runtest 
Building file: ../test.cpp
Invoking: GCC C++ Compiler
g++ -I"/home/david/test/googletesteclipse/googletest/googlemock/include" -I"/home/david/test/googletesteclipse/googletest/googletest/include" -O0 -g3 -Wall -c -fmessage-length=0 -MMD -MP -MF"test.d" -MT"test.o" -o "test.o" "../test.cpp"
Finished building: ../test.cpp
 
Building target: test
Invoking: GCC C++ Linker
g++ -L"/home/david/test/googletesteclipse/googletest/googlemock/make" -o "test"  ./test.o   -l:gmock_main.a -lpthread
Finished building target: test
 
make: *** [runtest] Error 1
./test
Running main() from gmock_main.cc
[==========] Running 1 test from 1 test case.
[----------] Global test environment set-up.
[----------] 1 test from xxx
[ RUN      ] xxx.yyy
../test.cpp:11: Failure
Failed
[  FAILED  ] xxx.yyy (0 ms)
[----------] 1 test from xxx (0 ms total)

[----------] Global test environment tear-down
[==========] 1 test from 1 test case ran. (0 ms total)
[  PASSED  ] 0 tests.
[  FAILED  ] 1 test, listed below:
[  FAILED  ] xxx.yyy

 1 FAILED TEST
../makefile.targets:2: recipe for target 'runtest' failed

20:41:16 Build Finished (took 544ms)
~~~~