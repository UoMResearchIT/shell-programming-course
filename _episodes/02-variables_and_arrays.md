---
title: "Variables and Arrays"
teaching: 20
exercises: 10
questions:
- "How can I store information without writing it to file"
objectives:
- "Learn how to create and reference a variable"
- "Learn how to create and reference and indexed array"
keypoints:
- "BASH variables are a useful tool for storing data"
- "BASH arrays are useful for indexed lists of things"
- "`{}` denotes a code block, useful for referencing arrays and variables"
- "`[@]` denotes all of an array, while `[X]` denotes the value at position `X`"
---

## Variables

In the shell novice course you were, indirectly, introduced to bash variables, when working
with loops: http://swcarpentry.github.io/shell-novice/05-loop/index.html

~~~
for thing in list of things
do
  echo $thing
done
echo 'variables persist:' $thing
~~~
{: .language-bash}

~~~
list
of
things
variables persist: things
~~~
{: .output}

Variables can also be defined directly:
~~~
echo $thing
thing=this
echo $thing
~~~
{: .language-bash}
~~~
things
this
~~~
{: .output}

> ## Exploring Shell Variables
>
> You can check if a variable exists, and what it's contents are, using the `declare -p`
> command. This will list all variables and functions in the environment, so to ease use
> of this output of this command it is recommended that you pipe the output to another
> command, such as `less` or `grep`, to search for the variable you require.
>
{: .callout}

To delete a variable you can use `unset`.
~~~
thing=stuff
unset thing
~~~
{: .language-bash}
Note that you do not reference the contents of the variable (using `$thing`) to do this,
instead you reference the variable directly (using `thing`).

> ## Referencing a non-existent variable
>
> What happens if you reference a variable which doesn't exist?
> e.g.
> ~~~
> thing=stuff
> unset thing
> echo $thing
> ~~~
> {: .language-bash}
>
> > ## Solution
> >
> > Referencing a non-existent variable simply returns an empty string, no error message
> > is given. This can aid in the smooth-running of a script, but can also create problems
> > if you are used to the error messages that languages such as python return after
> > referencing a non-existent variable. We will look later at how you can test for the
> > existence of a variables more safely.
> >
> {: .solution}
{: .challenge}

## Arrays

BASH objects do not have to only contain a single value, they can contain a list of
values instead.

To create an array object you can use the notation:
~~~
listthings=( these are my things )
~~~
{: .language-bash}
> ## Whitespace warning
>
> Note that the whitespace is important --- it is used to denote the breaks between
> individual values, as well as the start and end of the list. If you wish to have items
> which contain whitespace you will need to wrap these in quotation marks. e.g.
> ~~~
> listthings=( these "are my" things )
> ~~~
> {: .language-bash}
{: .callout}

The array object now contains an indexed list of values:
~~~
declare -p | grep -w listthings
~~~
{: .language-bash}
~~~
listthings=([0]="these" [1]="are my" [2]="things")
~~~
{: .output}

To access the contents of the array you should use the indexes, e.g. `[0]` or `[1]`. You
can also access all values using the index `[@]`. However, to make use of these indexes you
must use curly braces to delimit the variable name.
> ## Referencing Arrays
>
> What are the outputs, and why, from the following commands?
> 1. `echo ${listthings[1]}`
> 2. `echo $listthings[1]`
> 3. `echo ${listthings[@]}`
> 4. `echo $listthings`
> 5. `echo ${#listthings[@]}`
>
> > ## Solution
> >
> > 1. `are my`  This is the value at index 1
> > 2. `these[1]` This is the first (index 0) value in the array, followed by the string `[1]`
> > 3. `these are my things` This is all values within the array.
> > 4. `these` This is the first (index 0) value in the array.
> > 5. `3` This is the length of the array.
> >
> >
> {: .solution}
{: .challenge}



Arrays can be referenced using a for loop, as before:
~~~
for thing in ${listthings[@]}
do
  echo $thing
done
~~~
{: .language-bash}







{% include links.md %}

