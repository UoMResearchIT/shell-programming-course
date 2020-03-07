---
title: "BASH Logic and Maths"
teaching: 0
exercises: 0
questions:
- "How can we have responsive workflows on the BASH shell"
objectives:
- "Explain Logical operators, and demonstrate IF structures"
- "Detail Maths operators, and cover basic sequencing"
keypoints:
- "Logic is powerful, and helps make your scripts robust"
- "Sequences and Math are useful for organising your datastructures (which we will come back to later)"
---

## If Control Structure

Many workflows are not linear, and all workflows will have exceptional cases in which
operations should be halted (if, for example, a file is missing, or a calculation would
fail). Bash has the `if` control structure, which can be used to control your workflow
in these situations.

In it's most basic form, this control structure will test for a condition, then execute
a set of program statements if that condition is true.

~~~
if [[ 8 -gt 7 ]];then
  echo "well, that's obvious"
fi
~~~
{: .language-bash}

To make the structure more useful, we can add alternate tests, to be conducted in sequence
if the first fails, and a final default set of program statements which are executed if
all conditional statements fail.

~~~
value=7
if [[ $value -gt 8 ]];then
  echo "value is greater than 8"
elif [[ $value -lt 8 ]]; then
  echo "value is less than 8"
else
  echo "value must be 8"
fi
~~~
{: .language-bash}



In Bash there are three types of operators for these conditional expressions:
file, numeric, and non-numeric. Each will return true (0) if the condition is met,
or false (1) if the condition is not met.




| conditional expressions |
| object or non-numeric | numeric or boolean | description |
|----------|--------|--------|
| -e | | file exists |
| -d | | file is a directory |
| -f | | file is a regular file |
| -z | | variable exists |
| >  |  -gt | greater than |
| <  |  -lt | less than |
| >= |  -ge | greater than or equal to |
| <= |  -le | less than or equal to |
| == |  -eq | equal |
| != |  -ne | not equal |
|| ! | logical NOT |
|| && | logical AND |
|| \|\| | logical OR |















## Bash Maths


~~~
j=`expr $i + 1`
j=$(( $i + 1 ))
let j=i+1
j=`echo "$i + 1" | bc`
~~~
{: .language-bash}


~~~
  +  :  Addition
  -  :  Subtraction
  *  :  Multiplication
  /  :  Division
  ** :  Exponent
~~~
{: .source}


{% include links.md %}

