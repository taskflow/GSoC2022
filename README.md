# 2022 Google Summer of Code (GSoC) Overview

Parallel programming has advanced many of today's scientific computing projects to a new level.
However, writing a program that utilizes parallel and heterogeneous computing resources
is not easy because you often need to deal with a lot of technical details,
such as load balancing, programming complexity, and concurrency control.
Taskflow streamlines this process and helps you quickly create 
high-performance computing (HPC) applications with programming productivity.

As the user community of Taskflow continues to grow (e.g., over 1.5M downloads),
we have identified two specific projects to enhance Taskflow's infrastructure
to facilitate its research and development:

1. [Enhance Benchmarking Environment](#project-1-enhance-benchmarking-environment)
2. [Enhance Pipeline Infrastructure](#project-2-enhance-pipeline-infrastructure)

Timeline of GSoC is available [here](https://developers.google.com/open-source/gsoc/timeline).

# Project #1: Enhance Benchmarking Environment

The project aims to enhance the benchmarking environment of Taskflow by adding 
more realistic and reproducible testcases to the repository.

## Description

The current benchmarking environment of Taskflow has been narrow on
electronic design automation (EDA) problems. 
Most of these benchmarks are embedded in applications themselves and are difficult to reproduce.
Moreover, we need benchmarks from other applications, such as streaming, video processing,
and high-performance computing (HPC)
to make the broader computing community study the performance of Taskflow.
We have identified three specific tasks to enhance the benchmarking of Taskflow:

1. Implement the standard [PaRSEC](https://parsec.cs.princeton.edu/) benchmarks using Taskflow
2. Implement a standalone benchmark environment for our EDA applications ([OpenTimer](https://github.com/OpenTimer/OpenTimer), DREAMPlace, etc.)
3. Implement baseline methods using mainstream programming systems (OpenMP, TBB, StarPU, etc.)

## Expected Outcome

We will place the result in a new repository, namely `Benchmarks`, 
under the [Taskflow Organization](https://github.com/taskflow). 
The repository will contain comprehensive instructions for reproducing the results.
We will also encourage participants to publis these results in related parallel computing conferences
or arXiv journals.

## Skills Required/Preferred

Participants should have decent C++14/17 programming experience. 
Basic knowledge about parallelism is preferred.

## Possible Mentors

The [Taskflow Team](https://taskflow.github.io/taskflow/team.html) will mentor this project throughout the course of summer code.
Participants should expect weekly project meeting to sync up the progress.

## Expected Size of Project

We expect 350 hours for this project.

## Difficulty Level

We rate this project an *medium* level of difficulty. This project primarily focuses on 
*using* Taskflow to implement parallel algorithms and applications, rather than developing
the Taskflow core functionalities. 
Participants in this project will gain much practical and hands-on experience 
of parallel programming and underatand the pros and cons of mainstream parallel programming tools.

# Project #2: Enhance Pipeline Infrastructure

The project aims to enhance the the pipeline programming infrastructure of Taskflow by adding 
a data abstraction layer with high scheduling performance.

## Description

Taskflow has introduced a new *task-parallel* pipeline programming framework in [v3.3](https://taskflow.github.io/taskflow/release-3-3-0.html).
The current pipeline design is primitive and does not provide any data abstraction.
For many data-centric pipeline applications, this can be inconvenient as users need to repetitively create data arrays.
Therefore, the goal of this project is to derive a pipeline class with data abstraction
to streamline the implementation of data-centric pipeline applications. 
We have identified three specific tasks:

1. Implement a new pipeline class with a data abstraction layer
2. Implement unit tests 
3. Implement algorithms to avoid false sharing of data  

## Expected Outcome

The implementation will be integrated into the core of Taskflow as a new algorithm module,
together with its documentation in our [handbook pages](https://taskflow.github.io/taskflow/index.html).  
We will also encourage participants to publis these results in related parallel computing conferences
or arXiv journals.

## Skills Required/Preferred

Participants should have decent C++14/17 programming experience. 
Basic knowledge about pipeline parallelism is preferred.

## Possible Mentors

The [Taskflow Team](https://taskflow.github.io/taskflow/team.html) will mentor this project throughout the course of summer code.
Participants should expect weekly project meeting to sync up the progress.

## Expected Size of Project

We expect 350 hours for this project.

## Difficulty Level

We rate this project a *difficult* level of difficulty, since it involves both implementation and algorithm challenges.
However, participants in this project will not just learn how to implement a real module atop Taskflow
but also gain practical research knowledge about parallel scheduling algorithm.

