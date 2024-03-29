# Google SoC 2022 

General Items:
  + [ ] learn C++ by watching videos at [CppCon](https://www.youtube.com/user/CppCon/playlists) 

Scalable Data Pipeline Design
+ [ ] I think for scalable_data_pipeline using the pointer (void*)-based method is a good idea
  1. of course, you can have a look at the newest [std::any](https://en.cppreference.com/w/cpp/utility/any)
  2. however, I don't recommend using `std::any` because it is essentially another layer of virtual call
  3. why not we just combine everything together in your design to have just one virtual layer

## 09/08/2022 (regular meeting)

+ [ ] submit your evaluation by 9/12
  1. Task 1: give the RUL to your data_pipeline.hpp implementation 
    + Here is the [link](https://github.com/taskflow/taskflow/blob/master/taskflow/algorithm/data_pipeline.hpp)
  3. Task 2: create a folder under this GSoC2022 repo with the name "PARSEC-v3" and "PARSEC-v2" and document how to compile it and run it in the README
    + ferret can run (version 3.0)
    + dedup can run but sometimes it get segfault (version 2.0) - still document how to run it but mention in what occasion we will see segfault
        | ![](https://github.com/taskflow/GSoC2022/blob/main/dedup.png) |
        |:--:|
        | <b>performance on dedup (It can run now!)</b>|

## 09/01/2022 (regular meeting)

+ [x] collect data for ferret and compare runtim/memory between taskflow and tbb
  1. measure at different numbers of threads (1, 2, 4, 8) = number of lines
  2. organize data in a pptx/pdf
      | ![](https://github.com/taskflow/GSoC2022/blob/main/ferret.png) |
      |:--:|
      | <b>performance on ferret</b>|
+ [x] continue to make dedup work
  1. try to find the reason for segfault
  2. if 1 doesn't work, can we implement the tbb version on PARSEC 3.0 based on the implementation you had in PARSEC 2.1 (optimal)
  3. I think #2 is optimal because eventually you can release your code for other people to use and compare
  4. if none of the above work, we can implement the tbb version ourself ... (nested pipeline parallelism)

## 08/19/2022 (regular meeting)

+ [x] keep tracking of the error message of tbb implementation
+ [x] implement a taskflow version parsec and compare the result with the default version(pthread) on benchmark ferret

## 08/12/2022 (regular meeting)

+ [x] update your `data_pipeline.hpp` and documentation with the upstream dev branch
  + next week we will check in your code
+ [x] upload your newest experimental result slide
+ [ ] start setting up PARSEC benchmark
  1. read the [doc](https://parsec.cs.princeton.edu/download/tutorial/3.0/parsec-tutorial.pdf)
  2. focus on ferret and dedup
  3. run the script and study the TBB pipeline implementation
  4. implement a TBB equivalent using Taskflow (may take some time)

## 08/04/2022 (regular meeting)

+ [x] revised your documentation and in-code documentation

## 07/28/2022 (regular meeting)

+ [x] normal_vs_efficient => make plot to compare Taskflow with TBB
+ [x] start writing documentation for `tf::data_pipeline` based on the instructions [here](https://taskflow.github.io/taskflow/install.html#BAIBuildDocumentation)
  1. doxygen/algorithms/data_pipeline.dox
  2. add data_pipeline.dox in doxygen/Doxyfile
+ [x] synchronize your local repo with the remote (both dev and master)
+ [x] study taskflow's scheduling algorithm [here](https://tsung-wei-huang.github.io/papers/icpads20.pdf)

## 07/21/2022 (regular meeting)

+ [x] use profiler to find out why tbb is faster under 4 and 8 threads (cache hit rate?)
  1. this is a reseach question => find problem and then solve the problem
+ [x] rethink if we really need `char padding[CLPAD(sizeof(T))]` in your `struct padded`
  1. you can run the experiment again to see if the data is the same
  2. you can run `/bin/time` to check the memory
+ [x] rename `make_datapipe` to `make_data_pipe`
+ [x] upload your experiment data slide
+ [x] measure the memory data between taskflow and tbb using [/user/bin/time](https://stackoverflow.com/questions/774556/peak-memory-usage-of-a-linux-unix-process)
+ [x] we will go through the midterm evaluation using email when it is open

## 07/07/2022 (regular meeting)

+ [x] Try using a bigger data type (as opposed to int) in your `data_pipeline_dev.hpp`
+ [x] Modify your work() function to include frequent access to the referenced argument (data) to increase the effect of false sharing
  1. do not write things like static loops that compiler can optimize 
    + `for(int i=0; i<100; i++) data++; ` => `data += 100`
    + call some library functions such as `std::pow`
  2. make more experiments 
+ [x] try logging into the server
```
~$ ssh phrygiangates@twhuang-server-01.ece.utah.edu
```
+ [x] run your unittest with [thread sanitizer](https://clang.llvm.org/docs/ThreadSanitizer.html) enabled
```
cmake ../ -DCMAKE_CXX_FLAGS="-fsanitize=thread"
```

## 06/30/2022 (regular meeting)

+ [x] always use `std::decay_t` to store the data and pass the data in reference to user's callable
```cpp
#include <iostream>
#include <functional>

int main() {

  // user's perspective
  auto lambda = [] ( const int& )  {  };
  
  // your perspective - DataPipeline
  using C = decltype(lambda);
  using T = int;

  //make_datapipe<int&, std::string&> ==> make_datapipe<int, std::string>, here we always decay for storing the data
  static_assert(std::is_invocable_v<C, T&>, "");  // here, we always call user's callable passing the data by reference
}
```
+ [x] do more experiments to compare different combinations of s and p
  1. {ssss, sppp, ssssssss, sppppppp, ssssssssssssssss, sppppppppppppppp}
  2. num_lines == num_threads {4, 8, 16, 24}
  3. num_rounds should be at least 3 to amortize variation
  4. tbb vs taskflow (reference version)
  5. paste your figure over here
 
+ [x] think about why sppppp..pp is slower than ssssss..ss (ideally, more p should be faster)
```cpp
alignas(2*TF_CACHELINE_SIZE) int int_1_on_first_cacheline;    // accessing int_1 by thread 1 is guaranteed to be independent of accessing int_2 by thread2
alignas(2*TF_CACHELINE_SIZE) int int_2_on_second_cacheline;   
```

## 06/23/2022 (regular meeting)

+ [x] start to benchmakr your `tf::DataPipeline`
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
