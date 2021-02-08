---
title: "Advanced Loops"
teaching: 20
exercises: 0
questions:
- "How do you avoid repetition of code (but not actions) in your scripts?"
- "How can we repeat actions an indeterminate number of times, but not get stuck in infinity?"
objectives:
- "Explain While loops, and how to control their use"
- "Why is waiting good, and how is it best used?"
keypoints:
- "`seq [first] [increment] last` creates a sequence of (real) numbers"
- "`for` loops can be controlled using `seq` or C-style notation"
- "`${#array[@]}` is useful for setting these sequences"
- "`while` loops use conditional statements, and aren't fixed in length like `for` loops"
- "`while` loops cna be used for process control"
---


## Sequences and indexes for loops

As well as looping through arrays directly, we can also write loops which iterate through
series of integer values, which can then be used as indexes. This can be useful if you need
to add or remove matching data from a series of arrays, etc.

Two methods can be used to do this. The first is to use the `seq` command to build a
sequence of integer values, e.g.:
~~~
seq 4
~~~
{: .language-bash}
The universal format for this command is `seq [first] [increment] last`, where the last
value must be defined, but the incremental value is optional, as is the first value (but
this must be defined if the incremental value is defined). Note that other options can be
added, but these will be system-specific, and wont be covered here. The default values for both
the start and incremental values are 1. The sequence will be built according to these rules,
starting at the first value, and continuing until the next value created would greater than
the last value.

> ## sequences
>
> Please create a sequence of every 4th number between 3 and 37. Then create a sequence
> of these same numbers in reverse.
>
> > ## Solution
> >
> > `seq 3 4 37`
> >
> > `seq 35 -4 3` (Note: because `seq` starts from the first number given, you have to use
> >  _a priori_ knowledge to ensure that this sequence is the exact reverse of the
> >  first sequence).
> >
> {: .solution}
{: .challenge}


> ## real number maths
>
> Although we are not interested in this feature for the purposes of indexing arrays, it
> should be noted that `seq` uses real numbers, not integers, and so more complex sequences
> of numbers can be created than are shown here.
>
{: .callout}

To use `seq` within a `for` loop we must execute the command within it's own subshell, e.g:
~~~
for i in $(seq 1 31); do
  echo $i
done
~~~
{: .language-bash}
Trying to do this without the subshell will simply cause your loop to iterate through the
set of strings `seq 1 31`.

These integer values can be used for referencing items within an array - but do remember
that bash array indexes start at 0, and adjust your `seq` command accordingly:
~~~
varlist=( a list of strings )
for i in $(seq 0 3); do
  echo ${varlist[$i]}
done
~~~
{: .language-bash}

> ## Indexing lengths
>
> Because our index starts from 0, we must remember to end our loop at length-1.
> Not doing this in bash is unlikely to give a fatal error (unlike other, stricter,
> programming languages, for which it would cause a buffer overrun), but the empty
> string which is returned could cause issues for your workflow if you are not expecting
> it.
>
{: .callout}


`For` loops can be constructed using C-style notation too. These use a set of three
expressions (start conditional; end conditional; increment conditional) within a math
context, e.g:
~~~
varlist=( a list of strings )
for (( i=0; i<4; i++ )); do
  echo ${varlist[$i]}
done
~~~
{: .language-bash}
> ## C-style integer increments
>
> The code `(( i++ ))` increments the value of variable i by one, equivalent to using
> `(( i+=1 ))` or (more explicity) `(( i=$i+1 ))`. Negative increments can be performed
> by using `-` instead of `+`.
>
{: .callout}





> ## Looping through arrays without predetermined length
>
> Often we want to loop through arrays without a predetermined number of elements, so
> is useful to not have to hard-code the end number into the for loop.
>
> 1) Remembering that you can get the length of an array using `${#array[@]}`, and that
> integer maths should be carried out within an arithmetic expansion (`$(( ))`), please
> adapt the `seq` loop above so that it determines the array length automatically, and
> uses that within the for loop.
>
> 2) Please do the same using the C-style notation loop.
>
> 3) Please adapt the C-style notation loop to run through the array in reverse order.
>
> > ## Solutions
> >
> > 1)
> > ~~~
> > varlist=( a list of strings )
> > len=${#varlist[@]}
> > endvalue=$(($len-1))
> > for i in $(seq 0 $endvalue); do
> >   echo ${varlist[$i]}
> > done
> > ~~~
> > {: .language-bash}
> >
> > 2)
> > ~~~
> > varlist=( a list of strings )
> > len=${#varlist[@]}
> > for (( i=0; i<$len; i++ )); do
> >   echo ${varlist[$i]}
> > done
> > ~~~
> > {: .language-bash}
> >
> > 3)
> > ~~~
> > varlist=( a list of strings )
> > len=${#varlist[@]}
> > endvalue=$(($len-1))
> > for (( i=$len-1; i>=0; i-- )); do
> >   echo ${varlist[$i]}
> > done
> > ~~~
> > {: .language-bash}
> >
> >
> > Note: the lines creating variables `len` and `endvalue` could be incorporated directly into
> > the for loop statements, but they are explicitly stated here to make the solutions more
> > readable.
> >
> {: .solution}
{: .challenge}




## While loops

So far we have worked with fixed length loops, however not all processes are a fixed length,
and so a more open-ended solution is needed.

This is provided by the `while` loop, which uses a conditional statement to check when the
loop should be exited.

For example, this can be used to break the loop after user input:
~~~
halt=no
while [[ $halt != 'yes' ]]; do
  wait 3
  echo "break out of the loop?"
  read halt< /dev/tty
done
~~~
{: .language-bash}
Note the use of `wait`, to wait a given number of seconds before continuing with execution
of the loop.

It can also be used to replicate (in a more convoluted, and less maintainable, manner) the
for loops above:
~~~
varlist=( a list of strings )
len=${#varlist[@]}
i=0
while (( i<$len )); do
  echo ${varlist[$i]}
  (( i++ ))
done
~~~
{: .language-bash}

While loops are useful for process control: for automating the checking to see if processes
are finished, for example, and moving onto the next stage of the workflow once they are.

> ## Tracking program progress
>
> As an example of how `while` loops can be used to wait for a process to finish, we will
> create a function which waits for a random period before finishing. It writes it's status
> to a log file, which we can use to track the progress of the program.
> ~~~
> sleeptest () { echo 'started' > log.out ; sleep $(($RANDOM/1000)) ; echo 'finished' >> log.out ; }
> ~~~
> {: .language-bash}
>
> Tracking the current status of the program can be done using `tail`, e.g.:
> ~~~
> sleeptest &
> tail -1 log.out
> ~~~
> {: .language-bash}
> ~~~
> started
> ~~~
> {: .output}
> Using the `-1` flag tells `tail` to only return the last line of the file.
>
> Can you fill the three gaps in this `while` loop, so that it exits once the sleeptest
> function has ended?
> ~~~
> sleeptest &
> finished_tasks=0
> job_limit=1
> while [[ $finished_tasks ____ $job_limit ]]; do
>   sleep 3
>   finished_tasks=0
>   log_tail=$(______)
>   if [[ ______ ]]; then
>     echo "finished a task"
>     ((finished_tasks+=1))
>   else
>     echo "still going"
>   fi
> done
> ~~~
> {: .language-bash}
> > ## Solution
> > ~~~
> > sleeptest &
> > finished_tasks=0
> > job_limit=1
> > while [[ $finished_tasks -lt $job_limit ]]; do
> >   sleep 3
> >   finished_tasks=0
> >   LOG_TAIL=$( tail -1 log.out )
> >   if [[ $LOG_TAIL == "finished" ]]; then
> >     echo "finished a task"
> >     halt=yes
> >     ((finished_tasks+=1))
> >   else
> >     echo "still going"
> >   fi
> > done
> > ~~~
> > {: .language-bash}
> {: .solution}
{: .challenge}


{% include links.md %}

