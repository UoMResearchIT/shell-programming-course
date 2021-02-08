---
title: "Symbolic Links"
teaching: 10
exercises: 10
questions:
- "How can you reuse one file in multiple directories?"
- "How can you ease moving around your file system?"
objectives:
- "Detail symbolic links, and how they are used"
- "Learn when not to use symbolic links, and how to avoid pitfalls in using them."
keypoints:
- "Symbolic links to objects (files or directories) can be created using `ln -s`"
- "These are links to the object, not it's contents, so these can change or be deleted"
- "Symbolic links can cross physical disks, and so are useful in networked filesystems"
- "Caution must be exercised when following `..` paths across symbolic links"
- "They are most useful for linking to, and/or renaming, input and configuration files or directories"
---

A symbolic link, also called a soft link, is a pointer which enables you to find another
file, much like a shortcut in Windows. Like these it is useful for creating shortcuts within
the file system, for simplifying the file paths used by other programs, or easing your
navigation between different work directories in a networked system (important when
working on HPC systems).

It is important to remember that symbolic links do not point directly to any data that
might be in the target, they instead point to the file system itself. This allows you to
link to either files or directories using the same command, and also to link to filesystems
hosted on remote computers. But it also means that there is a high risk of data loss if the
remote files are moved or deleted. Because of this it is recommended that you use them
sparingly in your workflows.

We will use the dataset from the BASH introduction course to demonstrate the use of links.

A symbolic link can be created using the command:
~~~
cd ~/Desktop/
ln -s data-shell/molecules
ls -l molecules
~~~
{: .language-bash}
~~~
lrwxrwxrwx  1  user group 20 Feb  8 20:00 molecules -> data-shell/molecules/
~~~
{: .output}
This has created a symlink to the `molecules` directory, with the name `molecules`.
Like `cp`, `ln` will default to the given object name, but unlike the copy command it does
not need to be given a destination location.

You can identify symlinks by the `@` following their name if `ls -F` is used:
~~~
ls -F
~~~
{: .language-bash}
~~~
data-shell/ data-shell.zip molecules@
~~~
{: .output}

You can use `cd` to enter, and exit, this directory, as you would any other directory:
~~~
cd ~/Desktop/molecules
pwd
cd ..
pwd
~~~
{: .language-bash}
~~~
/home/jon/Desktop/molecules
/home/jon/Desktop
~~~
{: .output}
This is because the `cd` command is able to resolve, or track, the symlinks by processing
them after following the `..` path in the second `cd` command. We can disable this ability,
using the `-P` flag (which forces `cd` to resolve the symlinks to the original directory
structure before following `..` paths):
~~~
cd ~/Desktop/molecules
pwd
cd -P ..
pwd
~~~
{: .language-bash}
~~~
/home/jon/Desktop/molecules
/home/jon/Desktop/data-shell
~~~
{: .output}
In this case, even though we seemed to be in `~/Desktop/molecules`, using `..` while using
the `-P` flag takes us to the `~/Desktop/data-shell` directory, because that is the true
parent directory. This relationship is made explicitly clear if we use `-P` for the first
`cd` command:
~~~
cd -P ~/Desktop/molecules
pwd
cd ..
pwd
~~~
{: .language-bash}
~~~
/home/jon/Desktop/data-shell/molecules
/home/jon/Desktop/data-shell
~~~
{: .output}
In this case we arrive directly in the original directory with the first `cd` command,
meaning that it does not matter whether we use the `-P` flag or not for the second command,
we will always arrive back in the `data-shell` directory.

Shell intrinsic commands, such as `ls` and `pwd` are able to make use of the shell's tracking
of the symlink, so that they deal with the directory structure as we would expect. Commands
such as `ls` and `cp` are not able to do this, and so they always resolve symlinks to the
original directory structure before following `..` paths.

Because of this behaviour, it is advised that you avoid using `..` paths which cross a
symlink in your scripts - in this situation it would be safer to use the absolute path
(or a path relative to a fixed point, such as your home directory `~/`).

Symlinks can be removed without destroying the object they point to:
~~~
cd ~/Desktop
ls -l molecules
rm molecules
ls -ld data-shell/molecules
~~~
{: .language-bash}
~~~
lrwxrwxrwx 1 user group 20 Feb  8 19:58 molecules -> data-shell/molecules
drwxr-xr-x 2 user group 4096 Feb  8 11:36 data-shell/molecules
~~~
{: .output}


Although we can leave the name of the symlink the same as the original object, one of the
most useful features of symlinks is being able to rename files without moving or changing
the original file.

For example, in `data-shell/data/elements/` we have `xml` files describing each atom. Each
of these is named using the periodic table symbol, e.g. `N.xml` is the Nitrogen descriptor.
However, we have a program which is expecting the files to have the full atom name, e.g.
`Nitrogen.xml`. We can easily enough create these symlinks, e.g.:
~~~
cd ~/Desktop/data-shell/data
mkdir elements-fullnames
cd elements-fullnames
ln -s ../elements/N.xml Nitrogen.xml
ls -l
~~~
{: .language-bash}
~~~
lrwxrwxrwx 1 user group 17 Feb  8 21:32 Nitrogen.xml -> ../elements/N.xml
~~~
{: .output}
Doing this will enable us to use the program, without having to create all the input files
again. Do keep in mind though that, although you can delete symlinks without deleting the
original file, if a program tries to write to a symlink, it will write to the original file.
This method is suitable for easily replicating or renaming *input* files. Extreme caution
should be used if you use the same method for output or log files.


> ## Scripting the linking of all atom files.
>
> There are over 100 atom files in the `elements` directory, linking to each of these
> by hand would be quite painful. Fortunately these are text files, and each of them
> contains the full name of the element in the first line of the file, e.g.:
> ~~~
> head -1 ~/Desktop/data-shell/data/elements/N.xml
> ~~~
> {: .language-bash}
> ~~~
> <element name="Nitrogen"/>
> ~~~
> {: .output}
> To strip the atom name out of this string you can either use `sed`:
> ~~~
> head -1 ~/Desktop/data-shell/data/elements/N.xml | sed -E -e 's/^.*"([A-Za-z]*)".*$/\1/'
> ~~~
> {: .language-bash}
> ~~~
> Nitrogen
> ~~~
> {: .output}
>
> or  `cut` (by using `"` as the delimiter):
> ~~~
> head -1 ~/Desktop/data-shell/data/elements/N.xml | cut -d '"' -f 2
> ~~~
> {: .language-bash}
> ~~~
> Nitrogen
> ~~~
> {: .output}
>
> Please write a bash script which will use a `for` loop and one of these two methods to create
> links that use the full element names for these files within whatever directory it is run.
>
> > ## Solution
> >
> > ~~~
> > for orig_file in "${@}"
> > do
> >    element_name=$(grep -i 'name=' ${orig_file} | sed -E -e 's/^.*"([A-Za-z]*)".*$/\1/' )
> >    ln -s ${orig_file} ${element_name}.xml
> > done
> > ~~~
> > {: .language-bash}
> >
> > This script should be run using:
> > ~~~
> > bash link_script.sh ~/Desktop/data-shell/data/elements/*.xml
> > ~~~
> > {: .language-bash}
> >
> {: .solution}
{: .challenge}




{% include links.md %}

