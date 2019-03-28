---
title: "Benchmark .net core code"
date: 2019-03-28T13:11:19+01:00
subtitle: "Use the library BenchmarkDotNet to evaluate the performance .net core code"
author: Gabriele Teotino
tags: ["core", "benchmark"]
categories: ["development"]
draft: true
---

The library [BenchmarkDotNet](https://benchmarkdotnet.org/) does all the dirty job required for micro benchmarking. It setup a new project for each benchmark and run the project multiple times and inside each project will run also the method multiple times.

It prints a nice report and generates files for 

<!--more-->

## Installation

```shell
dotnet add package BenchmarkDotNet
```

A nice bonus is the added dependency of **CommandLineParser**, a very useful library.

## Preparation

Create a class for the benchmark

```csharp
namespace simpleproblems
{
    [MemoryDiagnoser]
    public class CubesumBenchmarks
    {
        [Benchmark(Baseline = true)]
        public void Precalculate()
        {
            Cubesum.Precalculate();
        }
    }
}
```

The attribute **MemoryDiagnoser** instructs the benchmark to collect also memory and GC usage.

## Run

The code has to be compiled in RELEASE

```shell
dotnet run -c Release
```
