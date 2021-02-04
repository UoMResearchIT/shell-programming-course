---
title: "Subshells and Functions"
teaching: 30
exercises: 20
questions:
- "How can you organise your code into functional blocks"
- "How can you import settings from other files into your scripts"
objectives:
- "Explain how BASH functions operate"
- "Explain the difference between local and global variables"
- "Explain how to import bash files into your environment"
keypoints:
- "environment variables are accessible by all programs run from that shell"
- "`export` turns a (private) shell variable into an environment variable"
- "`()` creates a subshell"
- "`{}` creates a code block within the current shell"
- "`$()` allows a subshell to be used for command substitution, for saving the output as a variable"
- "`<()` allows a subshell to be used for process substitution, for passing the output to another program"
- "`read var1 var2 <<<$()` can be used to save more than one output from a command substitution"
- "`function NAME() {}` creates a function code block"
- "code inside functions are not executed on creation, and can be used repeatedly after creation"
- "`. script.sh` and `source script.sh` enable the importing of code and variables from other scripts"
---



## Shell and Environment Variables

In the shell novice course you were introduced to writing and using shell scripts,
[http://swcarpentry.github.io/shell-novice/06-script/index.html](http://swcarpentry.github.io/shell-novice/06-script/index.html).

When a shell process is created, a corresponding environment is created to contain the
shell process. The environment contains information needed for the shell to interact with
the operating system (such as the location of your home directory, or which display is being
used). Any further processes created by commands run in the shell process will inhabit
this environment, and have access to this information too. This information is stored in
variables, similar to the variables introduced in the previous lesson, but these are
'environment' or 'global' variables, whereas the variables used previously were 'shell' or
'local' variables. The difference between these is that 'environment' variables are
readable by all processes in that environment, while 'shell' variables are unique
to that process which created them.

Invoking a shell script leads to the launch of a new process, which processes the commands
within that script. That process will not have access to the 'shell' variables of the
shell from which it was launched.

To demonstrate this we can create a simple bash script, called 'test.sh' and
containing this code:
~~~
echo $var1
echo $var2
~~~
{: .language-bash}
Then run this code:
~~~
var1=23
export var2=12
bash test.sh
~~~
{: .language-bash}
~~~

12
~~~
{: .output}
The `export` command turns the shell variable into an environment variable, enabling all
processes within that environment to access it.

> ## Exploring Environment Variables
>
> You can check what other environment variables have been set using the `declare -px`
> command.
>
{: .callout}

Environment variables are also accessible for other programs running the same environment.
E.g, for python these can be accessed using `os.environ`:
~~~
python -c "import os ; print(os.environ['var2'])"
~~~
{: .language-bash}


## Subshells

Subshells are separate instances of the command process, run as a new process, and defined
within your scripts using `()`. Because a subshell is run in a new process, these can be
used for parallel processing (although we will not cover that here). Here we will
introduce key concepts behind the use of subshells.

Unlike calling a shell script, subshells inherit the same variables as the original process,
and thus can access any of these (even those which have not been exported).
~~~
x=5 ; (echo $x)
~~~
{: .language-bash}
~~~
5
~~~
{: .output}
However these variables are copies, and modifications made to existing
variables, or newly created variables, will not be available to the original process.
~~~
x=5 ; (echo $x; x=3; y=2; echo $x $y) ; echo $x $y
~~~
{: .language-bash}
~~~
5
3 2
5
~~~
{: .output}

> ## Code blocks
>
> Code blocks can be denoted between curly brackets `{}` (as will be used later).
> However this does not launch a subprocess, and any changes to, or additions of, variables
> will be persistant.
>
> ~~~
> x=5 ; {echo $x; x=3; y=2; echo $x $y;} ; echo $x $y
> ~~~
> {: .language-bash}
> ~~~
> 5
> 3 2
> 3 2
> ~~~
> {: .output}
> Note that, unlike subprocesses, the final bracket needs to be on a new line, or separated
> by a `;` from the last command used.
>
{: .callout}

Because there is no persistence of variables outside of the subshell, to pass information
out from this process you have to either write to file, or use one of the two following
operations: command or process substitution.

## Command Substitution

Even though the syntax for a subshell is very similar to that for declaring an array, we
cannot directly save the subshell in the same manner, instead we have to use
command substitution. This is a feature which enables the recording of the output from an executed
command. The command is executed within a subshell, and the substitution is carried out
using the following notation:
~~~
x=4; echo $x; x=$(y=5;echo $x$y); echo $x
~~~
{: .language-bash}
~~~
4
45
~~~
{: .output}



> ## Command Substitution using backquotes
>
> Command substitution can also be carried out using backquotes \` \`. This is not
> directly analogous to `$()`, as some special characters will need escaping within the
> backquotes, but they behave in very similar manners. \` \` is the older implementation,
> and so will be common in script libraries, but is now deprecated, and the `$()` notation
> is recommended for all new scripts.
{: .callout}


> ## Example usage of command substitution
>
> Jon wants to save the year, month, and day, from the `date` function, as variables, so
> that he can use them later for running download scripts. Can you write some code using
> command substitution to do this?
>
> > ## Solution
> >
> > You will need three separate calls to the `date` function for this, one each for the
> > day, month, and year. E.g.:
> > ~~~
> > YEAR=$(date +%Y)
> > MONTH=$(date +%m)
> > DAY=$(date +%d)
> > ~~~
> > {: .language-bash}
> {: .solution}
>
> Now that he has the code for obtaining today's date, he needs to do the same for
> yesterday's date. Can you copy and adapt your code above to do this?
>
> > ## Solution
> >
> > Again you will need three separate calls to the `date` function for this,
> > one each for the day, month, and year. To these you will need to remove one day in each, E.g.:
> > ~~~
> > PREV_YEAR=$(date -d "-1 day" +%Y)
> > PREV_MONTH=$(date -d "-1 day" +%m)
> > PREV_DAY=$(date -d "-1 day" +%d)
> > ~~~
> > {: .language-bash}
> {: .solution}
{: .challenge}

> ## Using variables in an operational script
>
> Now that Jon has code for calculating today and yesterday's dates, he needs to add
> these to his download script. Please edit the `manunicast_download.sh` script, so that
> the web address passed to wget is for today's data.
>
> > ## Solution
> >
> > The web address is stored in the script as a simple string, so we can insert information
> > from the date variables directly into this.
> >
> > ~~~
> > YEAR=$(date +%Y)
> > MONTH=$(date +%m)
> > DAY=$(date +%d)
> >
> > PREV_YEAR=$(date -d "-1 day" +%Y)
> > PREV_MONTH=$(date -d "-1 day" +%m)
> > PREV_DAY=$(date -d "-1 day" +%d)
> >
> > WEBROOT="http://manunicast.seaes.manchester.ac.uk/charts/manunicast/"
> >
> > ADDRESS="${WEBROOT}${YEAR}${MONTH}${DAY}/d02/meteograms/meteo_ABED_${PREV_YEAR}-${PREV_MONTH}-${PREV_DAY}_1800_data.txt"
> > wget $ADDRESS
> > ~~~
> > {: .language-bash}
> > To make this code more readable, we have split out the fixed base of the address as
> > `WEBROOT`, you do not need to do this in your own code.
> >
> > Using `{ }` brackets around the variable name is good practice - avoiding any errors
> > in case you accidentally make another variable name with the following text. It is also
> > good practice to wrap the text string in `" "`, in case there are spaces in the string.
> >
> {: .solution}
{: .challenge}


## Process Substitution

Process substitution, using `<()`, is a feature which enables the usage of the output
of an executed command within another command, similar to the piping of output using `|`.

As a (rather artificial) example, this enables us to compare the contents of two variables.
Passing these variables directly, or via a command substitution leads to the shell searching
for a file named after the variable value, e.g.:
~~~
diff $(echo 3) 5
~~~
{: .language-bash}
~~~
diff: 3: No such file or directory
diff: 5: No such file or directory
~~~
{: .output}

Using process substitution ensures that the shell avoids this error:
~~~
diff <(echo 3) <(echo 5)
~~~
{: .language-bash}
~~~
1c1
< 3
---
> 5
~~~
{: .output}

And these subshell methods can be nested as required:
~~~
cat <(echo year is $(date +%Y)) <(echo month is $(date +%m)) <(echo day is $(date +%d))
~~~
{: .language-bash}
~~~
year is 2021
month is 02
day is 02
~~~
{: .output}




More information on, and examples of using, subshells are available here:
[https://www.tldp.org/LDP/abs/html/subshells.html](https://www.tldp.org/LDP/abs/html/subshells.html)





## Functions

Bash functions are, at their most basic level, labelled code blocks which enable the
repeated use of a set of commands. Their purpose is to make your code more readable and,
in avoiding writing the same code time and again, more maintainable.

Functions can be declared by either
~~~
functionname () {
  commands
}
~~~
{: .language-bash}
or
~~~
function functionname () {
  commands
}
~~~
{: .language-bash}

Functions must be declared before they are used, and as they are a simple code block within
the shell process, can read, modify, and create all variables within the shell.
~~~
var1='E'
examplechange () { var1='D'; }
echo $var1
examplechange
echo $var1
~~~
{: .language-bash}
~~~
E
D
~~~
{: .output}
Note that the code within the function is not executed until it is called.

Bash functions are more basic than those of other programming languages, and do not easily
lend themselves to modern functional programming practices. There are some tricks which
can help though.

Arguments can be passed to functions, in a similar manner as for scripts and programs, as
you learnt in the introduction to bash lessons:
~~~
readingargs () { echo $#; echo $1; echo $2; }
readingargs "arg1" "arg2"
~~~
{: .language-bash}
~~~
2
arg1
arg2
~~~
{: .output}

To avoid unintentional usage of variables created within a function, these can be set as
`local`, so that they are not available outside the function.
~~~
combineargs () { local var1=$1$2; echo $var1; }
var1="start"
var2="end"
combineargs $var1 $var2
echo $var1
~~~
{: .language-bash}
~~~
startend
start
~~~
{: .output}
Note that using the `local` declaration ensures that you don't overwrite any external
variables which share the same name.

To return information from a function in manner in which it can be allocated to an
arbitrary variable, it is best to use `echo` to output it, and then capture the result of
this.
~~~
funcresult="$(combineargs $var1 $var2)"
~~~
{: .language-bash}

Multiple values can be returned in a similar manner, using the `read` command to split
the output.
~~~
returnargs () { echo $1; echo $2; }
read arg1 arg2 <<<$(returnargs "start" "end")
echo $arg1
echo $arg2
~~~
{: .language-bash}

> ## Output Warning
>
> What is the output from the following command? And why is it as it is?
>
> ~~~
> read arg1 arg2 <<<$(returnargs "start here" "end")
> echo $arg1
> echo $arg2
> ~~~
> {: .language-bash}
>
> > ## Solution
> >
> > The first word is stored in `arg1`, while the last two words are stored in `arg2`.
> >
> > ~~~
> > start
> > here end
> > ~~~
> > {: .output}
> >
> > Be aware that the outputs from a function are separated only by whitespace, so if any
> > exists within an outputted variable then this will cause problems for the read command.
> > In addition, if there are more items returned than expected, then any extra items will
> > be appended to the last argument in the list.
> {: .solution}
>
{: .challenge}

> ## Return Values
>
> An arbitrary status can be returned from a function (using `return $arg1`), however this
> is best saved for error checking your processes, rather than for passing results out from
> the function.
>
{: .callout}


> ## Functionalising the Date commands
>
> Jon wants to use the date commands that he has been given in later scripts, so he has
> created the following function template. Can you complete this, and replace the date
> code in the script with a call to the function?
>
> ~~~
> determine_next_date () {
>     # This function determines the next date, using a specified offset.
>     # usage: determine_next_date [+/-] [#days] [current year] [current month] [current day]
> }
> ~~~
> {: .language-bash}
>
> > ## Solution
> >
> > ~~~
> > determine_next_date () {
> >     # This function determines the next date, using a specified offset.
> >     # usage: determine_next_date [+/-] [#days] [current year] [current month] [current day]
> >
> >     local next_year=$( date -d "$3$4$5 $1 $2 day" +%Y )
> >     local next_month=$( date -d "$3$4$5 $1 $2 day" +%m )
> >     local next_day=$( date -d "$3$4$5 $1 $2 day" +%d )
> >
> >     echo $next_year $next_month $next_day
> > }
> > ~~~
> > {: .language-bash}
> >
> > ~~~
> > read year month day <<<$(determine_next_date - 1 $year $month $day)
> > ~~~
> > {: .language-bash}
> >
> {: .solution}
{: .challenge}


## Sourcing Bash Scripts

Rather than simply calling one bash script from another, with the limitations described
above, code and resources from bash scripts can be imported into another script using
the `source` functionality. This can take the form:
~~~
. script.sh
~~~
{: .language-bash}
or:
~~~
source script.sh
~~~
{: .language-bash}

All code within that script will be executed at the point it is sourced, so there are
limitations in how this can be used. It is most useful for loading key variables, or for
configuring the environment or loading functions (as is done in the `.bash_profile` and
`.bashrc` files).


> ## Splitting off the date math function
>
> In order that the `determine_next_date` function can be used in other scripts, please
> move it to a new script, called `function_date_math.sh`, source this script in your
> original file, and then make sure it still works as before.
>
> > ## Solution
> >
> > Example scripts for linux and OSX are included in the `date_math_function` directory,
> > called `functions_date_math_linux.sh` and `functions_date_math_osx.sh`.
> >
> {: .solution}
{: .challenge}


{% include links.md %}


