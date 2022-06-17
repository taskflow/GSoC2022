# Google SoC 2022 

General Items:
  + [ ] learn C++ by watching videos at [CppCon](https://www.youtube.com/user/CppCon/playlists) 

## 06/16/2022 (regular meeting)

+ [ ] changed your `tf::DataPipe` class interface to include three template arguments, Input, Output, C
+ [ ] write a helper function, `tf::make_data_pipe` to avoid partial compilation issue using the example below:

```
#include <iostream>
#include <string>

template <typename A, typename B, typename C>
struct DataPipe {
  DataPipe(int type, C&& c) : _callable {std::forward<C>(c)}{
  }
  C _callable;
};

template <typename A, typename B, typename C>
auto make_data_pipe(int type, C&& callable) {
  return DataPipe<A, B, C>(type, std::forward<C>(callable));
}

// zhicheng's idea: can we further avoid typename A and typename B?
// However, this will make your function_traits really complicated if you want to support all callable types:
// 1. function pointer
// 2. class object with () defined
// 3. lambda function object (implementation-defined)
// 4. std::function (dynamic polymorphism)
template <typename C>
auto make_data_pipe_2(int type, C&& callable) {
  using B = typename function_traits<C>::result_type;
  using A = typename function_traits<C>::get_type<0>
  return DataPipe<A, B, C>(type, std::forward<C>(callable));
}

int main() {
  //DataPipe<int, std::string> foo(2, [](){});
  auto foo = make_data_pipe<int, std::string>(2, [](){});
  //foo._callable();
  tf::DataPipeline dp{ num_lines,
    make_data_pipe<int, std::string>(2, [](int){ return "mystring"s; });
    ...  
  //  zhicheng's idea
  //  make_data_pipe_2(2, [](int){ return "mystring"s; });
  //  ...
  }
}
```
+ [ ] update your present codebase with this new change
+ [ ] complete the unittest (based on `pipelines.cpp`)

+ [ ] study the interface of `tf::ScalablePipeline` and create a `tf::ScalableDataPipeline`
  + study `examples/parallel_scalable_pipeline.cpp`
  + study `unittests/scalable_pipelines.cpp`
  + study the documentation of [scalable pipeline](https://taskflow.github.io/taskflow/ParallelScalablePipeline.html)

```cpp
tf::ScalableDataPipeline <???> // what would be a good interface for this!?
```

## 06/09/2022 (regular meeting)

- [x] fixed the compilation issue (why can't it be complied when changing the lambda argument to `std::string&`?)
- [x] Extend the interface to still allow passing `tf::Pipeflow&`

```cpp
// first pipe
DataPipe <void, int> { tf::PipeType::SERIAL, []( tf::Pipeflow& ){ }}

// other pipes
DataPipe <int, std::string> { tf::PipeType::SERIAL, []( int, tf::Pipeflow& pf ){} } 
DataPipe <int, std::string> { tf::PipeType::SERIAL, []( int ){} }  // your current unittest
```


## 06/02/2022 (regular meeting)

- [x] have a look at `examples/data_parallel_pipeline.cpp` 
  + complete a shitty design first for tf::DataPipeline in `taskflow/algorithm/pipeline.hpp`
  + complete a shitty design first for tf::DataPipe in `taskflow/algorithm/pipeline.hpp`
  + you can enable the compilation of data_parallel_pipeline in `examples/CMakeLists.txt`
  + you may not need to derive your tf::DataPipeline from tf::Pipeline for now; duplicate the code and modify it is ok at this stage
- [x] start thinking about unittest 
  + take a look at [doctest](https://github.com/doctest/doctest)
  + take a look at taskflow/unittests/pipelines.cpp
  + create a unittest unittests/data_pipelines.cpp and start writing some simple and complex unittests
  + make sure you add the data_pipelines.cpp unittest into unittests/CMakeLists.txt

```
~$ cd build
~$ make
~$ make test
~$ ./unittests/parallel_pipelines -d yes
~$ ./unittests/parallel_pipelines -tc="UnittestName"
``

## 05/27/2022 (regular meeting)

- [x] start tracing the pipeline code `taskflow/algorithm/pipeline.hpp`
- [x] work on dev branch first
