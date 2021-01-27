---
title: "Dates, Scheduling, and Downloading Files"
teaching: 0
exercises: 0
questions:
- "How can we deal with date maths on the command line"
- "How can we schedule regular compute jobs"
- "How can we download files outside of a web-browser"
objectives:
- "Understanding some of the tools available for addressing regular needs."
keypoints:
- "There are tools for automating most tasks from the command line."
---

## Date maths



## Scheduling tasks with CRON

Often workflows need to be repeated at regular intervals - checking for updates in source data, running maintenance tasks, or producing regular updates to services. These tasks can be automated using the [cron](https://en.wikipedia.org/wiki/Cron) job scheduler. Cron is available on most UNIX based systems (although, on many HPC platforms, access to cron will be blocked for ordinary users), you can find out if you have it installed (and what tasks you have scheduled) using the `crontab` (cron table) command:
~~~
crontab -l
~~~
{: .language-bash}

To configure cron it is easiest to create a configuration file, which will be read simply using the command:
~~~
crontab [config.txt]
~~~
{: .language-bash}
Each line of this file will represent a job, and will look like:
~~~
# ┌───────────── minute (0 - 59)
# │ ┌───────────── hour (0 - 23)
# │ │ ┌───────────── day of the month (1 - 31)
# │ │ │ ┌───────────── month (1 - 12)
# │ │ │ │ ┌───────────── day of the week (0 - 6) (Sunday to Saturday;
# │ │ │ │ │                                   7 is also Sunday on some systems)
# │ │ │ │ │
# │ │ │ │ │
# * * * * * <command to execute>
~~~
This notation can be a little confusing to use, but online tools such as [crontab.guru](https://crontab.guru/) are available for checking your configurations, to ensure that cron will run jobs at the times you expect.

> ## Scheduling jobs
>
> Jon wants to set two data download tasks, one to run at 2:45 am every Monday,
> and one which runs at 2:00 pm on the 1st and 15th of each month. What notation
> should he use to set these jobs ([crontab.guru](https://crontab.guru/) can be
> used to work this out).
>
> > ## Solution
> >
> > `45 2 * * 1` - 2:45 am on every Monday
> >
> > `00 14 1,15 * * ` - 2pm on the 1st and 15th of the month
> >
> {: .solution}
{: .challenge}





## Downloading using wget

Often workflows involve input or source file downloads, and it is useful if these can be
automated on the command line, rather than relying on using a web-browser for this.

[Wget](https://www.gnu.org/software/wget/) enables the retrieval of files using the widely
used HTTP, HTTPS, FTP, and FTPS protocols (much as most web-browsers do). It has many
features, such as being able to resume aborted downloads, using filename wild cards and
recursive searches, and use of timestamps to determine if documents need redownloading.
Here we will just cover the basic usage though, for more details and advanced usage check
the [GNU Wget documentation](https://www.gnu.org/software/wget/manual/).


The basic usage is to simply give the path to a file that you wish to download. If it
exists, and is accessible, then wget will download it and save it in your local directory.
E.g.:
~~~
wget http://manunicast.seaes.manchester.ac.uk/charts/manunicast/20200105/d02/meteograms/meteo_ABED_2020-01-04_1800_data.txt
~~~
{: .language-bash}
This can be used for directories as well as files, but when used on a directory what is
returned is a file listing the directory contents (as you would see in a web-browser).
E.g.:
~~~
wget http://manunicast.seaes.manchester.ac.uk/charts/manunicast/20200105/d02/meteograms
~~~
{: .language-bash}

To download all the contents of a directory you need to use the recursive `-r` flag. In
most cases this should be combined with the `-np` flag, which tells wget that you don't
want it to crawl up to parent directories. You can also restrict the files you download
based on their file-extension string using the `-A` flag.

For example, to download a single file use:
~~~
wget -r -np http://manunicast.seaes.manchester.ac.uk/charts/manunicast/20200105/d02/meteograms/meteo_ABED_2020-01-04_1800_data.txt
~~~
{: .language-bash}
This creates a directory structure following the http path structure, containing the file you requested.

To download all the text (.txt) files in that directory use:
~~~
wget -r -np -A txt http://manunicast.seaes.manchester.ac.uk/charts/manunicast/20200105/d02/meteograms/
~~~
{: .language-bash}
This creates the same directory structure, but it will contain all the text files in that
directory.


> ## Restricting webcrawling to a single domain
>
> Although it is not important for our interest in downloading data from internet
> archives, when downloading data from a website it can be useful to ensure that wget
> doesn't follow links off that site. This can be done using the `-D [domain]` flag, where
> [domain] is the web domain that you wish to restrict wget to. In the examples given
> above we could add `-D manchester.ac.uk` if we wished to restrict searches to the
> University of Manchester domain, or even `-D manunicast.seaes.manchester.ac.uk` if we
> wished to restrict searches to the ManUniCast site only.
>
{: .callout}

> ## Using wildcards in file names
>
> The HTTP protocol does not support wildcards, so using wildcards would not work in the
> examples given above. Wget can also use the FTP protocol though, which does support the
> use of wildcards, so if you are downloading data from an FTP server these are an option.
>
{: .callout}


> ## Scheduling jobs
>
> Create a simple bash script which will use wget to download a (single) file from the
> ManUniCast archive. Then create a crontab file to submit this to cron, scheduling the
> task for only 3-4 minutes in the future (so that you can see it work). After you've seen
> that it works, remember to clear your cron setup, so it does not run again at the same
> time tomorrow.
>
{: .challenge}


{% include links.md %}
