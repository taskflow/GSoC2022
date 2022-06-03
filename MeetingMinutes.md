## 06/02/2022 (regular meeting)

- [ ] have a look at `examples/data_parallel_pipeline.cpp` 
  + complete a shitty design first for tf::DataPipeline in `taskflow/algorithm/pipeline.hpp`
  + complete a shitty design first for tf::DataPipe in `taskflow/algorithm/pipeline.hpp`
  + you can enable the compilation of data_parallel_pipeline in `examples/CMakeLists.txt`
  + you may not need to derive your tf::DataPipeline from tf::Pipeline for now; duplicate the code and modify it is ok at this stage
- [ ] start thinking about unittest 
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
