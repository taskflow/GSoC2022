# 2022 Google Summer of Code (GSoC) Overview

Parallel programming has advanced many of today's scientific computing projects to a new level.
However, writing a program that utilizes parallel and heterogeneous computing resources
is not easy because you often need to deal with a lot of technical details,
such as load balancing, programming complexity, and concurrency control.
Taskflow streamlines this process and helps you quickly create 
high-performance computing (HPC) applications with programming productivity.

Timeline of GSoC is available [here](https://developers.google.com/open-source/gsoc/timeline).

# Projects

We have identified two specific projects to enhance Taskflow:

1. [Enhance Benchmarking Environment](#enhance-benchmarking-environment)
2. [Enhance Pipeline Infrastructure](#enhance-pipeline-infrastructure)

## Enhance Benchmarking Environment

The current benchmarking environment of Taskflow has been narrow on
electronic design automation (EDA) problems. 
To make the broader computing community easy to study the performance of Taskflow,
we have identified three specific tasks to enhance the benchmarking of Taskflow:

1. Implement the standard [PaRSEC](https://parsec.cs.princeton.edu/) benchmarks using Taskflow
2. Implement a standalone benchmark environment for our EDA applications 
3. Implement baseline methods using mainstream programming systems (OpenMP, TBB, StarPU, etc.)

We will place the result in a new repository, namely `Benchmarks`, 
under the [Taskflow Organization](https://github.com/taskflow). 
Publications in related conferences or arXiv journals are possible.

Expected time commitment: 350 hours

## Enhance Pipeline Infrastructure

Taskflow has introduced a new *task-parallel* pipeline programming framework in [v3.3](https://taskflow.github.io/taskflow/release-3-3-0.html).
The current pipeline design is primitive and does not provide any data abstraction.
For many data-centric pipeline applications, this can be inconvenient as users need to repetitively create data arrays.
Therefore, the goal of this project is to derive a pipeline class with data abstraction
to streamline the implementation of data-centric pipeline applications. 
We have identified three specific tasks:

1. Implement a new pipeline class with a data abstraction layer
2. Implement unit tests 
3. Implement algorithms to avoid false sharing of data  

The implementation will be integrated into the core of Taskflow as a new algorithm module.
Publications in related conferences or arXiv journals are possible.

Expected time commitment: 350 hours

# Mentors

The [Taskflow Team](https://taskflow.github.io/taskflow/team.html) will mentor both projects throughout the course of summer code.
Participants should expect weekly project meeting to sync up the progress.

# Eligibility

Individuals who are interested in our projects should have basic knowledge about C++ programming and parallel programming.
A degree of computer science or related subject will be a plus.
