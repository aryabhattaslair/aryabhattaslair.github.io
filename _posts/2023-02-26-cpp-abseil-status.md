## Post 2: ABSL! ABSL! Please give me your Status!

Continuing my wild ride into the C++Verse. I have stumbled across another great library Abseil <https://abseil.io/>. 

While working on an application, I needed to add error handling to my projects as the state space exploded. Abseil::Status is a great module for it. It is also something used ubiquitously at Google.

Lets look at the function **Status** which allows providing status of the task required from a function. We will take a simple toy example which may not excite you but the example is simple to just focus on the library.

In this example, we check whether a file exists and display error message accordingly.

#### using absl::Status to return status of a file

```cpp
#include <iostream>
#include <string>
#include <fstream>
#include "absl/status/status.h"
#include "absl/strings/str_cat.h"


absl::Status AbseilOpen(std::string filename) {
  std::ifstream file;
  file.open(filename);
  if (file) {
    file.close();
    return absl::OkStatus();
  } else {
    return absl::NotFoundError(
        absl::StrCat(filename, " does not exist"));
  }
}


int main() {
  std::string filename = "data/echo.txt";
  absl::Status s = AbseilOpen(filename);
  if (absl::IsNotFound(s)) {
    std::cout << s.message() << "\n";
  } else {
    std::cout << absl::StrCat(filename, " Hurrah!") << "\n";
  }
}

```
As can be seen here we ask the function (AbseilOpen) to do a task and check the status using absl::Status object returned. Status returns RPC error codes which can be seen here <https://github.com/googleapis/googleapis/blob/master/google/rpc/code.proto>.

In the above example, we were asking the function to perform a status. However lets say we want the function to return and object and also the status/error if there is an error.

This is where absl::StatusOr<T> comes to the rescue 

#### Reading and Writing to/from file

```cpp
#include <iostream>
#include <string>
#include <fstream>
#include "absl/status/statusor.h"
#include "absl/strings/str_cat.h"
 
absl::StatusOr<int> GetCount(int input) {
  if (input == 0) {
    return absl::InvalidArgumentError(absl::StrCat("Invalid ", input));
  } else {
    return input^2;
  }
}

int main() {
  std::string input;
  std::cout << "Type Input" << std::endl;
  std::getline(std::cin, input);
  int updated_total = 0;
  absl::StatusOr<int> i = GetCount(std::stoi(input));
  if (i.ok()) {
    updated_total += *i;
  }
  std::cout << "Updated Value: " << updated_total;
}

```

As can be seen the value of int is returned if all conditions are met else an error code is returned. I will be using this library to make my application more robust.
I installed the abseil using its Github library. Detailed instructions to do this are available here <https://abseil.io/docs/cpp/quickstart-cmake.html#getting-the-abseil-code>.

This is how my cmakefile looks
  
 ```cpp
cmake_minimum_required(VERSION 3.22)

project(TestingAbseil)

set(CMAKE_CXX_STANDARD 14)

# Import Abseil's CMake targets
add_subdirectory(abseil-cpp)

add_executable(TestingAbseil main.cpp)

# Declare dependency on the absl::strings and absl::status library
target_link_libraries(TestingAbseil absl::strings absl::status)
 ```
  
Take care and keep learning. Will see you soon.

