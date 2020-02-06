---
title: "Advanced Loops"
teaching: 0
exercises: 0
questions:
- "How do you avoid repetition of code (but not actions) in your scripts?"
- "How can we repeat actions an indeterminate number of times, but not get stuck in infinity?"
objectives:
- "Explain While loops, and how to control their use"
- "Why is waiting good, and how is it best used?"
keypoints:
- "While loops enable you to repeat actions as often as needed (without predetermination of the steps)"
- "Wait is a useful command for managing sub processes."
---


## Sequences and indexes for loops

~~~
for i in `seq 1 31`; do
  echo $i
done
~~~
{: .language-bash}

~~~
varlist=( a list of strings )
for i in `seq 0 3`; do
  echo ${varlist[$i]}
done
~~~
{: .language-bash}

~~~
varlist=( a list of strings )
for i in `seq 0 ${#varlist[@]}`; do
  echo ${varlist[$i]}
done
~~~
{: .language-bash}

~~~
varlist=( a list of strings )
len=${#varlist[@]}
for (( i=0; i<$len; i++ )); do
  echo ${varlist[$i]}
done
~~~
{: .language-bash}



~~~
varlist=( a list of strings )
len=${#varlist[@]}
for (( i=$len-1; i>=0; i-- )); do
  echo ${varlist[$i]}
done
~~~
{: .language-bash}






## While loops

~~~
halt=no
while ( [ $halt != 'yes' ] ); do
  echo "break out of the loop?"
  read halt< /dev/tty
done
~~~
{: .language-bash}


{% include links.md %}

