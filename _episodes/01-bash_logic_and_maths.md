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

## If Construct

~~~
if [ 8 -gt 7 ];then
  echo "well, that's obvious"
fi
~~~
{: .language-bash}


~~~
value=7
if [ $value -gt 8 ];then
  echo "value is greater than 8"
elif [ $value -lt 8 ]; then
  echo "value is less than 8"
else
  echo "value must be 8"
fi
~~~
{: .language-bash}


~~~
  -gt : >  : greater than
  -lt : <  : less than
  -ge : >= : greater than or equal to
  -le : <= : less than or equal to
  -eq : == : equal
  -ne : != : not equal
  -e  :    : file exists
  -d  :    : file is a directory
  -f  :    : file is a regular file
~~~
{: .source}


## Bash Maths


~~~
j=`expr $i + 1`
j=$(( $i + 1 ))
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

