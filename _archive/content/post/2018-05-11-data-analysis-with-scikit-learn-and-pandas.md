---
title: "Data analysis with scikit learn and pandas"
date: 2018-05-07T15:15:56+02:00
subtitle: "Using scikit learn to data mine a small dataset of clinical exams."
author: Gabriele Teotino
tags: ["anaconda", "conda", "pandas", "scikit", "scikit learn", "svm", "numpy"]
categories: ["datascience"]
draft: false
---

Download [Miniconda](https://conda.io/miniconda.html) 64bit version with python 3.6.

## Installation
Install Miniconda using the current user (no sudo)
```shell
cd ~/Downloads/
bash Miniconda3-latest-Linux-x86_64.sh
# reload env (or restart terminal)
source ~/.bashrc
```

This toolset is my only use for anaconda, so everithing is installed in the default environment.

Install all the tools.
```shell
# this will also install numpy
conda install scikit-learn
conda install ipython
conda install matplotlib
# this will also install pandas
conda install seaborn
conda install jupyter
```

## Workspace
Create a folder for notebooks and data files, then start ipython.

```shell
mkdir ~/datascience/tesigiulia -p
conda activate
jupyter notebook --notebook-dir='~/datascience/tesigiulia'
```

Now a new browser window will open at the url http://localhost:8888/tree

## Notebook
