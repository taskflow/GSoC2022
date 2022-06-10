# Google SoC 2022 

General Items:
  + [ ] learn C++ by watching videos at [CppCon](https://www.youtube.com/user/CppCon/playlists) 

## 06/09/2022 (regular meeting)

- [ ] fixed the compilation issue (why can't it be complied when changing the lambda argument to `std::string&`?)
- [ ] Extend the interface to still allow passing `tf::Pipeflow&`

```cpp
// first pipe
DataPipe <void, int> { tf::PipeType::SERIAL, []( tf::Pipeflow& ){ }}

// other pipes
DataPipe <int, std::string> { tf::PipeType::SERIAL, []( int, tf::Pipeflow& pf ){} } 
DataPipe <int, std::string> { tf::PipeType::SERIAL, []( int ){} }  // your current unittest
```

- [ ] complete the unittest (based on `pipelines.cpp`)
- [ ] study the interface of `tf::ScalablePipeline` and create a `tf::ScalableDataPipeline`
  + study `examples/parallel_scalable_pipeline.cpp`
  + study `unittests/scalable_pipelines.cpp`
  + study the documentation of [scalable pipeline](https://taskflow.github.io/taskflow/ParallelScalablePipeline.html)

```cpp
tf::ScalableDataPipeline <???> // what would be a good interface for this!?
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
