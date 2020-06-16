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



### Unix Date Math (move this to separate lesson?)

Many environmental datasets include date information - either from the timeseries data that
is being processed, or for the workflow management. On UNIX systems the `date` program
can be used to calculate dates, and determine incremental changes in date and time (it can
also be used to change the system date - but we will try to avoid doing this today). These
calculations can be relative to the current time, or derived from a pre-defined date.

The basic usage is, simply:
~~~
date
~~~
{: .language-bash}
This outputs a string containing the current date and time. The output string can be formatted
using a number of different flags, prefixed by `+`. To print out just the year we can use:
~~~
date +%Y
~~~
{: .language-bash}
To print out the year and current number of seconds (admittedly an illogical combination
of units), use:
~~~
date +'%Y %S'
~~~
{: .language-bash}
Note that the format option takes a string, and other characters can be included if you
enclose the string in quotation marks (all formatting commands will be preceded by %).

> ## Formatting date strings
>
> More information on the formatting that can be applied to the date string can be found
> in the man page (`man date`). Please use this information to write a format string to
> output the current date as the string:
>
> 'hour, minutes, day of week, month, year (only the last 2 digits)'
>
> > ## Solution
> >
> > [need string format here!]
> >
> {: .solution}
{: .challenge}


So far we have used the system date as the basis of our output - we can change this, both
absolutely (by changing the reference date we start with), and relatively (by specifying
a desired offset to that date). The flags used to do this can vary from system to system,
however, as different implementations of the Unix standards were made by different
development groups. The two systems you are most likely to encounter are linux-based systems
(such as debian, and SE-linux), and the freeBSD Unix-based OS-X system. These use quite
different syntaxes, so we will cover these below. Be aware that other systems may have
different implementations, if you find that your system is different then consult the man
pages to get the correct syntax.

> ## Avoiding changing the system date
>
> The `date` command can also be used to change the system date. To avoid doing this in
> your scripts follow these (system specific) guidelines:
>
> For *linux* based systems the default behaviour is to not set the system date. If you
> wanted to change this use the `-s` or `--set` flags (don't do this for this course though).
>
> For *freeBSD* based systems (such as *OS-X*) the default is to set the system date. To avoid
> this when specifying different dates you must include the `-j` flag in your command.
>
{: .callout}

#### linux date changes

We can specify relative changes to the date, using the `-d` flag, e.g.:
~~~
date -d "+ 1 day"
~~~
{: .language-bash}
This takes a direction (+ or -), amount, and units of change. For example:
~~~
date -d "- 32 seconds"
~~~
{: .language-bash}



This flag is also used to specify an absolute date, e.g:
~~~
date -d "20180617"
~~~
{: .language-bash}
Note that the date format used here is YYYYMMDD (and that day and month portions must be
two digits long).

Other string formats can be used, e.g.:
~~~
date -d "17 June 2018"
~~~
{: .language-bash}
Note that some care is needed in crafting these strings, for example `"2018 06 17"` will
not work, neither will `"2018 June 17"` (though `"June 17 2018"` will, following the US
date standards). We will only use the YYYYMMDD format here.

These absolute and relative changes can be combined in the same string, e.g.:
~~~
date -d "20180617 + 1 day"
~~~
{: .language-bash}

And to print out only part of the date we can use the same output format commands as
before, e.g.:
~~~
date -d "20180617 + 1 day" +'%Y %m'
~~~
{: .language-bash}




#### OS-X (freeBSD) date changes

We can specify relative changes to the date, using the `-v` flag, e.g.:
~~~
date -v +1d
~~~
{: .language-bash}
This takes a direction (+ or -), amount, and units of change. For example:
~~~
date -v -32S
~~~
{: .language-bash}



To specify an absolute date we have to use the `-j -f` flags, and provide two strings,
the first for formatting information, the second for the date input, e.g:
~~~
date -j -f "%Y %m %d" "2018 06 17"
~~~
{: .language-bash}
Note that the date formats are similar to those used for formatting the output strings.
In this instance we do not need to use 2-digit strings for the day and month values.
To use other string formats change the formatting commands within the string.

These absolute and relative changes can be combined in the same command, e.g.:
~~~
date -v +1d -j -f "%Y %m %d" "2018 06 17"
~~~
{: .language-bash}

And to print out only part of the date we can use the same output format commands as
before, e.g.:
~~~
date -v +1d -j -f "%Y %m %d" "2018 06 17" +'%Y %m'
~~~
{: .language-bash}






{% include links.md %}

