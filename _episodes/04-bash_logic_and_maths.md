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

## Integer Maths

Bash variables are, in the general context, treated as strings. Integer maths can be
carried out using 'arithmetic expansion', using a math context to tell the interpreter
when to do this. The recommended method for this is using `$(( ))`:
~~~
i=3
j=$(($i + 1))
~~~
{: .language-bash}

Alternatively a command can be used to create the math context - these follow the same
rules as `$(( ))`, but give an exit status (which will be useful later).
~~~
a=3
((a=$a+7))
((a+=7))
~~~
{: .language-bash}
Within the math context the syntax and semantics of C's integer arithmetic are used,
enabling shorthands such as `+=` or `-=` for incremental changes in a variable.

> ## let, expr and bc
>
> Other programs are available for the execution of integer math in bash. For example:
> ~~~
> j=$(expr $i + 1)
> let j=i+1
> j=$(echo "$i+1" | bc)
> ~~~
> {: .language-bash}
> These follow the same integer math rules, however because they do not use the math
> context that `$(( ))` and `(( ))` provide (which protects, for example, against
> whitespace errors), they can be more difficult to work with, and so wont be covered here.
>
{: .callout}

> ## maths test
>
> enter a list of maths questions here
>
> > ## Solution
> >
> > enter the solutions, and explanations here
> >
> {: .solution}
{: .challenge}


~~~
  +  :  Addition
  -  :  Subtraction
  *  :  Multiplication
  /  :  Division
  ** :  Exponent
~~~
{: .source}

## Integer / String Conversions

A lot of information strings containing digits, such as date strings, step numbers, etc,
use leading zeros to ensure regularity of string length, as well as for sorting purposes.
This habit, however, can cause problems during when carrying out maths operations, e.g.:
~~~
x=05
y=08
echo $(($x+$y))
~~~
{: .language-bash}
~~~
-bash: 05+08: value too great for base (error token is "08")
~~~
{: .output}

This occurs because bash interprets values starting with `0` as base 8 integers (and
values starting with `0x` as base 16 integers) instead of the base 10 integers which we
generally use for maths.

To force an integer to be interpreted in base 10 we can prepend `10#` to the value, e.g.:
~~~
x=05
y=08
echo $((10#$x+10#$y))
~~~
{: .language-bash}
~~~
13
~~~
{: .output}
Note that this will not work for values which start with `+` or `-`, so cannot be used to
set the base of negative numbers. This is not, generally, and issue for date maths, but
could be for other uses. In these situations it would be wiser to use regular expression
substitutions in sed, as will be covered in a later lesson.


To create a string with leading zeros from an integer the `printf` (formatted print) program.
The format string used is `%X.Yi`, where `X` is the width of the string (optional), and
`Y` is the number of digits to use, e.g.:
~~~
day=5
printf "day number %.2i\n" $day
~~~
{: .language-bash}
~~~
day number 05
~~~
{: .output}



> ## date maths test
>
> enter a problem for date math here
>
> > ## Solution
> >
> > enter the solutions, and explanations here
> >
> {: .solution}
{: .challenge}


## If Control Structure

Many workflows are not linear, and all workflows will have exceptional cases in which
operations should be halted (if, for example, a file is missing, or a calculation would
fail). Bash has the `if` control structure, which can be used to control your workflow
in these situations.

In it's most basic form, this control structure will test for a condition, then execute
a set of program statements if that condition is true. For example:
~~~
if [[ 8 > 7 ]];then
  echo "8 is greater than 7"
fi
~~~
{: .language-bash}
and:
~~~
if [[ b > a ]];then
  echo "b is greater than a"
fi
~~~
{: .language-bash}
These are lexicographic comparisons of the strings, using the alphabetical order. Other
non-numeric conditions available are `<`, `>=`, `<=`, `==` and `!=` (described in the
table at the end of this section):
~~~
if [[ b != a ]];then
  echo "b is not the same as a"
fi
~~~
{: .language-bash}


For arithmetic comparisons inside `[[ ]]` brackets we instead would use the operators `-gt`,
`-lt`, `-le`, `-ge`, `-eq` and `-ne`:
~~~
if [[ 8 -gt 7 ]];then
  echo "8 is greater than 7"
fi
~~~
{: .language-bash}
For arithmetic expressions we can also use `(( ))`, as described in the section above:
~~~
if (( 8 > 7 ));then
  echo "8 is greater than 7"
fi
~~~
{: .language-bash}
These, confusingly, use the same operators as the non-numeric conditions in the `[[ ]]`
environment, rather than being the same as the numerical operators.


> ## Logical Test
>
> Which of these conditionals are True, which are False, and which don't work?
>
> 1. `[[ 4 -eq 4 ]]`
> 2. `[[ be == eb ]]`
> 3. `[[ 4 == 4 ]]`
> 4. `[[ 4 == 04 ]]`
> 5. `(( 4 == 04 ))`
> 6. `(( 4 -eq 04 ))`
>
> > ## Solution
> >
> > enter the solutions, and explanations here
> >
> {: .solution}
{: .challenge}


> ## Arithmetic Comparisons of Strings
>
> If you try to make an arithmetic comparison of strings then the `[[ ]]` command converts
> any non-numeric strings to variable names.
> ~~~
> if [[ "b" -gt "a" ]];then
>  echo "this is slightly less obvious"
> fi
> ~~~
> {: .language-bash}
{: .callout}





> ## One bracket or two?
>
> `[[ ]]` is a, relatively, new command, which is not universally available in other,
> non-bash, shells. Previous to this there was `[ ]` (synonymous for the `test` command),
> which is more portable than `[[ ]]`, but is not as powerful or robust. `[ ]` wont be
> covered here - more information on the differences between these can be found here:
> http://mywiki.wooledge.org/BashFAQ/031
{: .callout}




To make the structure more useful, we can add alternate tests, to be conducted in sequence
if the first fails, and a final default set of program statements which are executed if
all conditional statements fail.

~~~
value=a
if [[ $value > g ]];then
  echo "value is greater than g"
elif [[ $value < g ]]; then
  echo "value is less than g"
else
  echo "value must be 8"
fi
~~~
{: .language-bash}


There are three types of operators for these conditional expressions:
file, numeric, and non-numeric. Each will return true (0) if the condition is met,
or false (1) if the condition is not met. These operators are slightly different for
`[[ ]]` and `(( ))` expression testing, as shown in the following two tables.


| conditional expressions for `[[ ]]` |
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


| conditional expressions for `(( ))` |
| numeric or boolean | description |
|--------|--------|
| >  | greater than |
| <  | less than |
| >= | greater than or equal to |
| <= | less than or equal to |
| == | equal |
| != | not equal |
| ! | logical NOT |
| && | logical AND |
| \|\| | logical OR |
















{% include links.md %}

