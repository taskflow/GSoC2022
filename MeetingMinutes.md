# Google SoC 2022 

General Items:
  + [ ] learn C++ by watching videos at [CppCon](https://www.youtube.com/user/CppCon/playlists) 

## 06/23/2022 (regular meeting)

+ [ ] start to benchmakr your `tf::DataPipeline`
  1. is std::variant good enough for holding the data without any issue?
  2. create a new benchmark folder in `taskflow/benchmarks/data_pipeline` (take a look at `taskflow/benchmarks/linear_pipeline`)
  3. add the corresponding section in `benchmarks/CMakeLists.txt` to compile your data_pipeline benchmark (see the [tutorial here](https://taskflow.github.io/taskflow/BenchmarkTaskflow.html))
  4. think about a benchmark design of up to 16 pipes with some types you want to benchmark (e.g., int->std::string->...->std::vector<int>)
  5. implement both your `tf::DataPipeline` and `tbb::parallel_pipeline`  
  6. try to generate some plots and paste them here
    ![](https://github.com/taskflow/GSoC2022/blob/main/num_threads%3D24%20num_rounds%3D1%20num_lines%3D8%20pipes%3Dssssssss.png)
    ![](https://github.com/taskflow/GSoC2022/blob/main/num_threads%3D24%20num_rounds%3D1%20num_lines%3D8%20pipes%3Dsppppppp.png)
  
+ [ ] study the interface of `tf::ScalablePipeline` and create a `tf::ScalableDataPipeline`
  + study `examples/parallel_scalable_pipeline.cpp`
  + study `unittests/scalable_pipelines.cpp`
  + study the documentation of [scalable pipeline](https://taskflow.github.io/taskflow/ParallelScalablePipeline.html)

```cpp
#include <iostream>
#include <string>
#include <vector>

// Library developer's view
class ScalableDataPipeBase {

  public:

  ScalableDataPipeBase(size_t data_size) {
    _data_size = data_size;
  }

  virtual ~ScalableDataPipeBase() = default;

  virtual void process_pipe() {
      std::cout << "call virtual fun from base\n"; 
  }
  
  size_t _data_size;
};

class ScalableDataPipeline {
  public:
  std::vector< ScalableDataPipeBase* > pipes;

  void run() {
    for(auto & p : pipes) {
      p->process_pipe();
    }
  }
  
  
};

template <typename T>
class ScalableDataPipe : public ScalableDataPipeBase {

  public:

  ScalableDataPipe () :
    ScalableDataPipeBase(sizeof(T)) {
  }

  void process_pipe() override {
    if constexpr (std::is_same_v<T, int>) {
      std::cout << "call virtual fun from derived with T=int " 
                << "now I know your data size is " << _data_size << '\n';
    }
    else {
      std::cout << "call virtual fun from derived with T!=int "
                << "now I know your data size is " << _data_size << '\n';
    }
  }
};

// user's view

//template <typename T>
//auto make_scalalbe_data_pipe(...) {
//}

int main() {

  auto pipe1 = new ScalableDataPipe<int>();
  auto pipe2 = new ScalableDataPipe<std::string>();

  ScalableDataPipeline pl;

  pl.pipes.push_back(pipe1);
  pl.pipes.push_back(pipe2);

  pl.run();

  return 0;
}
```

## 06/16/2022 (regular meeting)

+ [x] changed your `tf::DataPipe` class interface to include three template arguments, Input, Output, C
+ [x] write a helper function, `tf::make_data_pipe` to avoid partial compilation issue using the example below:

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
+ [x] update your present codebase with this new change
+ [x] complete the unittest (based on `pipelines.cpp`)


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
