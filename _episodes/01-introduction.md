---
title: "Introduction"
teaching: 0
exercises: 0
questions:
- "How can we build workflows on the BASH shell"
objectives:
- "Overview of the course, explain what we are doing today"
keypoints:
- "Scripting your workflow enables reproducibility, and reduces errors"
---

From the bash introduction course you have learnt some of the basics of how to use this
shell. In this course we will learn about the more advanced methods available in bash,
which are useful for building more complex pipelines for your work. We will also look at
some of the computational environments for running your tasks in parallel: how to use these,
and manage multiple threads.

Many of the tools that you will be taught about today have more advanced corresponding
tools in other languages. However, bash is commonly used for process control on unix systems,
and these tools are widely availability on such systems, so it is useful for you to know
how to use them.


### Overview of research workflows

- data collection
- data processing
- results collating

Requirements:
- reproducibility (record keeping of process)
- robustness of procedure (reduced errors)
- reduced effort

Key aspects:
- record keeping (configuration files)
- progress and error checking (robust workflows)
- output collation
- report writing






### Jon's Meteorological Records: A Typical Problem

This is the testcase that we are going to use for teaching these processes to you today.

Jon is a new meteorologist, tasked with working on meteogram data sampled from meteorological
simulations for the whole of the UK. He has a few examples of these data samples from
which to learn about the data format, and design a processing pipeline for extracting statistical
information from this data. He will then apply this pipeline to processing a larger
archive of meteogram data (once he has collected it).

Start in the directory `data\meteogram_data` --- give quick introduction to the data.

Introduction to existing data tools available to Jon?

Today we will look at:
- data investigation
- processing datasets
- retrieving more data, and automating the analysis of this data

We will work through the process of building






{% include links.md %}

