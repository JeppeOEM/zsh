# Lecture 2: UNIX


## What Makes An Operating System

An *operating system* (OS) as we think of it today is a set of software that sits between the hardware and general application software.
The operating system provides *application programming interfaces* (APIs) to the application programs for operations such as memory allocation, input/output (I/O) operations, etc.

|![How an operating system relates to hardware, applications, and users](https://upload.wikimedia.org/wikipedia/commons/e/e1/Operating_system_placement.svg)|
|:--:|
|*Figure 2.1: How an operating system relates to hardware, applications, and users. From Wikipedia*|

Before the idea of a general operating system, applications needed to be customized to each hardware platform individually, because they needed to communicate differently with different hardware platforms.

The UNIX operating system was in fact born to fix that specific issue: as the catalog of application software grew, it became more efficient to fit an intermediate layer to the hardware, that would then allow all the applications to run independently of the specific hardware.

An operating system can be divided into the following separate layers:

- Kernel: the kernel is responsible for direct hardware communication, memory management, process management, file management, preemptive multitasking
- CLI: a *command line interface*, allows users to interact with the operating system via commands and scripts
- GUI: a *graphical user interface*, provides a user-friendly interface to the operating systems, often using a desktop metaphor
- Applications: e.g. word processors, editors, browsers

At the very least an operating system must have a kernel.

In this lecture we will mostly be concerned with the CLI.


## What Is A UNIX-like System?

A UNIX-like system is a computer operating system that behaves similar to a UNIX operating system.

For historical and commercial reasons not all UNIX-like systems are UNIX systems - e.g. we consider Linux a UNIX-like system.


## A Brief History of UNIX and UNIX-like Systems

UNIX was created in the early 1970s at *Bell Labs*, a research laboratory owned by the telephone company *AT&T*.

Starting out as a research project, UNIX was eagerly shared between Bell Labs and several American universities.
As it became clear that UNIX was an extremely powerful operating system, AT&T wanted to commercialize UNIX, so everyone running UNIX would have to pay a license fee to AT&T, and a very hefty one at that.

Other organizations, most notably Berkeley University, had made significant contributions to UNIX at this point, and was not ready to pay AT&T for what had effectively become an open source project at this point.

The response to the commercialization of UNIX was *Unix* (uncapitalized).

Unix is not a single system, but a family of UNIX-like system: they behave as UNIX, but they share no code with AT&T's commercial part of UNIX.

|![UNIX History](https://upload.wikimedia.org/wikipedia/commons/c/cd/Unix_timeline.en.svg)|
|:--:|
|*Figure 2.2: A not all-up-to-date history of the Unix operating system. From Wikipedia.*|

During the 1980s intense battles was fought over the licensing of UNIX, and what was part of UNIX and Unix, respectively.
As a consequence, several mutually incompatible versions of Unix was launched during the 1980s and 1990s.


## Why Learn Unix?

There are several good reasons why you, as a web developer, should master Unix:

- Almost all of the Internet runs off Linux and Unix
- The Internet is built around Unix, so the way the Internet works is a result of how Unix works
- Knowing Unix will make you a better web developer
- It's just better than everything else
- It could save your life if you ever get trapped in a Jurassic theme park

|[![It's UNIX](https://www.globalnerdy.com/wordpress/wp-content/uploads/2018/02/its-a-unix-system-i-know-this.jpg)](https://youtu.be/dFUlAQZB9Ng?t=2)|
|:--:|
|*Figure 2.3: Girl's knowledge of UNIX saves lives.*|


## Command Line Tools

Let's get started with the command line tools.


### The Unix Prompt

When you first open a Unix-like terminal you will see something like this:

```bash
henrik@minime in ~ $
```

> In this day an age you are unlikely ever going to work with an actual Unix *terminal*. A Unix terminal is a piece of hardware, i.e. display and keyboard. Instead, you will probably be working with a *terminal emulator*, i.e. a piece of software that ... well, emulates ... a Unix terminal. We often use the term terminal when we mean terminal emulator, exactly because you probably will never use an actual terminal.

What you see here is the *terminal prompt*.
Yours might look a bit different, but the basic idea is the same.

First, there's an indication of my *username* `henrik`.
This is useful, especially for technical users that might use several usernames for different purposes.

Next, the system tells me which system I'm logged into: `minime` in this case.
Again, I might work on many different machines, and I might not be working on the machine in front of me!

Next, the system shows me that I am in my *home directory*: `~`.
Generally, all users have their own home directory where they can store files.

Finally there is the prompt itself: `$`.
The dollar sign indicates that I am a regular user.
Or rather, that's the case in some *shells*, like the `bash` shell.
If you are using a `zsh` shell, you will probably see a `%` prompt instead.
If I was logged in as the `root` user I would normally get a `#` prompt instead.

> **Terminal** vs **Shell**: A *shell* is a piece of software that runs within the terminal emulator.
    There are several different shells: `ash`, `bash`, `fish`, `zsh`, etc.
    They do the same thing to some extend, but there are differences, and commands and scripts from one shell might need to be modified to work in a different shell.
    There is a set of standards, known as **POSIX** that works across most shells, but not `fish`.


### Remote Access The KEA Development Server

We are going to do most work on the KEA development server.
First, we need to log in to the remote server:

```bash
henrik@minime in ~ $ ssh kea-dev
```

If you have followed the instructions for setting up a KEA Development server this should be all you need.

You should now see a welcome message on the KEA development server:

```text
henrik@minime: ~
% ssh kea-dev

  __  __  ______  ____        _____   ______  __    _
 |  |/ / |   ___||    \  ___ |     \ |   ___|\  \  //
 |     \ |   ___||     \|___||      \|   ___| \  \//
 |__|\__\|______||__|\__\    |______/|______|  \__/


kea-dev:~%
```

This prompt looks a bit different, but the idea is the same: your are on the `kea-dev`

> Note that even though your development server is located in Frankfurt, it works just as if it was standing right in front of you.


### Keeping Up To Date

Just like your normal computer needs to update its software now and then, so does the development server.

Enter the command as shown below to update the development server:

```bash
kea-dev:~$ sudo apk update && sudo apk upgrade

fetch http://dl-cdn.alpinelinux.org/alpine/v3.12/main/x86_64/APKINDEX.tar.gz
fetch http://dl-cdn.alpinelinux.org/alpine/v3.12/community/x86_64/APKINDEX.tar.gz
fetch http://dl-cdn.alpinelinux.org/alpine/edge/main/x86_64/APKINDEX.tar.gz
fetch http://dl-cdn.alpinelinux.org/alpine/edge/community/x86_64/APKINDEX.tar.gz
fetch http://dl-cdn.alpinelinux.org/alpine/edge/testing/x86_64/APKINDEX.tar.gz
v3.12.3-78-gdafee4ec1f [http://dl-cdn.alpinelinux.org/alpine/v3.12/main]
v3.12.3-77-g89584230ef [http://dl-cdn.alpinelinux.org/alpine/v3.12/community]
v3.13.0-712-g179a4441eb [http://dl-cdn.alpinelinux.org/alpine/edge/main]
v3.13.0-724-gc7ae4ce473 [http://dl-cdn.alpinelinux.org/alpine/edge/community]
v3.13.0-721-gb1270ad068 [http://dl-cdn.alpinelinux.org/alpine/edge/testing]
OK: 30558 distinct packages available
(1/11) Upgrading busybox (1.32.1-r2 -> 1.33.0-r0)
Executing busybox-1.33.0-r0.post-upgrade
(2/11) Upgrading ssl_client (1.32.1-r2 -> 1.33.0-r0)
(3/11) Upgrading busybox-suid (1.32.1-r2 -> 1.33.0-r0)
(4/11) Upgrading ncurses-terminfo-base (6.2_p20210116-r0 -> 6.2_p20210123-r0)
(5/11) Upgrading ncurses-libs (6.2_p20210116-r0 -> 6.2_p20210123-r0)
(6/11) Upgrading busybox-doc (1.32.1-r2 -> 1.33.0-r0)
(7/11) Upgrading libcap (2.46-r0 -> 2.47-r0)
(8/11) Upgrading py3-setuptools (51.3.3-r0 -> 52.0.0-r0)
(9/11) Upgrading libcap-doc (2.46-r0 -> 2.47-r0)
(10/11) Upgrading py3-pip (20.3.3-r0 -> 20.3.4-r0)
(11/11) Upgrading py3-pip-doc (20.3.3-r0 -> 20.3.4-r0)
Executing busybox-1.33.0-r0.trigger
OK: 742 MiB in 267 packages

kea-dev:~$
```

Your output will look different depending on which updates are available at this point in time.
You should keep your system up-to-date by running these commands on a regular basis.

> A word on security: the KEA development server is set up to be very secure. However, all computer systems must be updated regularly to remain secure.


### Exploring The File System


#### The Home Directory

Let's take a look at what is in our home directory:

```bash
kea-dev:~$ ls
vps-reference-config
kea-dev:~$
```

We use the `ls` command to *list* files.

If you followed the instructions for setting up the KEA development server you should see a folder named `vps-reference-config` in your home folder, because we used `git` to clone the repository during installation.

There are actually more files than this, but these files do not show up by default.

We can use the `-la` options to show *long listing* and *all files*:

```bash
kea-dev:~$ ls -la
total 84
drwxr-sr-x    8 henrik   henrik        4096 Jan 25 11:22 .
drwxr-xr-x    3 root     root          4096 Jan 10 11:05 ..
-rw-------    1 henrik   henrik       12243 Jan 25 13:51 .ash_history
drwxr-sr-x    3 henrik   henrik        4096 Jan 10 11:22 .cache
-rw-r--r--    1 henrik   henrik         151 Jan 10 12:26 .gitconfig
-rw-r--r--    1 henrik   henrik          21 Jan 10 11:59 .profile
drwx------    2 henrik   henrik        4096 Jan 10 11:29 .ssh
-rw-------    1 henrik   henrik       30885 Jan 25 11:22 .viminfo
drwxr-sr-x    5 henrik   henrik        4096 Jan 10 11:10 vps-reference-config
    ^             ^        ^            ^    ^              ^
    |             |        |            |    |              |
 permissions    user     group       bytes   |            filename
                                          last modification
```

The file names are in the right-most column.
Files beginning with `.` are *hidden* files, and that's why they didn't show up in the first listing.

The file named `.` is the *current directory*, and as you can see it's owned by `henrik:henrik`.
The `d` in the *permissions* column indicates that this is a directory rather than a normal file.

The `drwxr-sr-x` permissions reads: `d`: directory, `rwx`: user (owner) read & write & execute, `r-s`: group read & setuid (run as user), `r-x`: others read & execute.

Note that the *parent* directory `..` is owned by `root`.


#### Moving Up

We can move up one directory level by changing the directory to the parent directory:

```bash
kea-dev:~$ cd ..
kea-dev:/home$
```

As you can see we are no longer in our home directory `~`, but in `home`, which is a directory that holds all the home directories for ordinary users.

We can list the content:

```bash
kea-dev:/home$ ls -la
total 12
drwxr-xr-x    3 root     root          4096 Jan 10 11:05 .
drwxr-xr-x   22 root     root          4096 Jan 10 11:03 ..
drwxr-sr-x    7 henrik   henrik        4096 Jan 25 13:53 henrik
kea-dev:/home$
```

You can see that this directory `.` is owned by `root`, just as we saw from `~` by looking at `..`.


#### The Root Directory

Again, let's go up one level:

```bash
kea-dev:/home$ cd ..
kea-dev:/$
```

We are now in the `/` directory, which is the *root* directory - not to be confused with the `root` user or the `root` directory!

If we now list the file system there is quite a lot here:

```bash
kea-dev:/$ ls -la
total 92
drwxr-xr-x   22 root     root          4096 Jan 10 11:03 .
drwxr-xr-x   22 root     root          4096 Jan 10 11:03 ..
drwxr-xr-x    2 root     root          4096 Jan 25 12:48 bin
drwxr-xr-x    3 root     root          4096 Jan 23 11:16 boot
drwxr-xr-x   11 root     root          2400 Jan 23 11:16 dev
drwxr-xr-x   38 root     root          4096 Jan 25 12:48 etc
drwxr-xr-x    3 root     root          4096 Jan 10 11:05 home
drwxr-xr-x    9 root     root          4096 Jan 23 11:16 lib
drwx------    2 root     root         16384 Dec 16 20:04 lost+found
drwxr-xr-x    5 root     root          4096 Dec 16 20:04 media
drwxr-xr-x    2 root     root          4096 Dec 16 20:04 mnt
drwxr-xr-x    3 root     root          4096 Jan 10 11:19 opt
dr-xr-xr-x  138 root     root             0 Jan 10 11:19 proc
drwx------    3 root     root          4096 Jan 23 11:31 root
drwxr-xr-x    9 root     root           460 Jan 23 11:16 run
drwxr-xr-x    2 root     root         12288 Jan 23 11:16 sbin
drwxr-xr-x    2 root     root          4096 Dec 16 20:04 srv
drwxr-xr-x    2 root     root          4096 Jan 10 11:03 swap
dr-xr-xr-x   13 root     root             0 Jan 10 11:19 sys
drwxrwxrwt    4 root     root          4096 Jan 25 13:49 tmp
drwxr-xr-x   10 root     root          4096 Jan 10 11:13 usr
drwxr-xr-x   13 root     root          4096 Jan 10 11:12 var
kea-dev:/$
```

We're not going to go through all of these, these are the most important:

- `bin`: contains *binaries*, that is, programs that all users can run
- `boot`: contains files necessary for booting the computer - **do not touch**
- `dev`: device files (drivers)
- `etc`: configuration files for the machine
- `home`: home directories for ordinary users
- `lib`: library files, files for kernel drivers such as video drivers
- `media`: this is a relatively new thing, contains auto mounted devices such as USB disks
- `mnt`: manually mounted file systems
- `opt`: some software that you install via compilation ends up here
- `proc`: system processes - in Unix everything is a file, even a process
- `root`: the `root` user's home directory
- `run`: run-time files for processes - **do not touch**
- `sbin`: like `/bin`, but only for the `root` user
- `usr`: a bit of a mess - users' home folders used to be here, now mainly user installed content
- `srv`: server files - this is a good place to put your *html* files
- `sys`: system resources - you can find information on devices and much more
- `tmp`: temporary files
- `var`: variable content, mostly used for logs

#### Navigating The File System

Now that you are in the root folder, or any other folder, a quick way to get to your home folder:

```bash
kea-dev:/$ cd
kea-dev:~$
```

The `cd` command without arguments bring you back to your home folder.

You can also get back to the *last* folder using:

```bash
kea-dev:~$ cd -
/
kea-dev:/$
```

You can also change to any folder using its *absolute path*:

```bash
kea-dev:~$ cd /usr/local/bin
kea-dev:/usr/local/bin$
```

Finally, you can use a *relative path*:

```bash
kea-dev:/usr/local/bin$ cd ../lib/
kea-dev:/usr/local/lib$
```


## Aliases

In the examples above we used `ls -la` to get a more useful output.
If you don't want to type that again and again you can make an *alias* that contains the options, so you can customize your commands.

It's not recommended to change the way the command work by default, as this could break some more fragile scripts.
Instead, you can come up with you own new name.
A popular name for a customized `ls` command is `ll`, because it's easy to type.

To get a custom `ll` command you can type: `alias ll='ls -halp'`.
Now typing `ll` gives you a very nice file listing.


## `.zshrc`, `.zshenv`, etc.

If you open up a new terminal on `kea-dev` you will see that your alias is gone.
This is because the alias only works in the current session.

> By default, your `kea-dev` server has been configured to use the `zsh` shell.
    The configuration files for this shell is named `.zshrc`, `.zshenv`, and there are several others.
    If you use a different shell these files will have different names.
    You can see a list of the files and what goes where here: [https://unix.stackexchange.com/a/71258](https://unix.stackexchange.com/a/71258).

To make customizations persistent you can add them to your `.zshenv` file.
This file may or may not exist in your `~` folder - if not, just make a new one:

```bash
kea-dev:~% vim .zshenv
```

Hit the `i` key to start typing, and type the alias command at the first line.

To exit and save the file press `ESC` and type `:wq` followed by `ENTER`.
Next time you open a terminal the alias will be available.

> You can *source* the `.zshenv` when you make changes, so you don't have to start a new terminal for changes to take effect: `source .zshenv`.


## Pimp My Prompt

Try add this line to your `.zshrc` to make your prompt more interesting:

```bash
PS1="$(whoami)@$(uname -n):%~ %# "
```

You can do all kinds of things: add colors, special characters, etc.
Search the Internet for inspiration.


## Redirections and Pipes

The CLI (Command Line Interface, another term for the shell) is not limited to running single commands.
The commands become a lot more powerful when we combine them, and almost all commands in Unix-like systems are prepared for this.

In general, commands accept input via `stdin` (*standard input*), sends output to `stdout` (*standard output*), and any errors that might be to `stderr` (*standard error*).

First, we will create a temporary directory where we can play with some files without messing things up.

```bash
kea-dev:~$ mkdir tmp
kea-dev:~$ cd tmp
kea-dev:~/tmp$
```

The `mkdir` command creates a new directory in the home folder (we have `~` in the prompt), and `cd` changes into the directory.

```bash
kea-dev:~/tmp$ echo "Hello world!"
Hello world!

kea-dev:~/tmp$ echo "Hello world!" > hello.txt
kea-dev:~/tmp$ cat hello.txt

Hello world!
```

The `echo` command outputs text - in the first example the text will be sent to `stdout`, in this case the terminal.

In the second example we use the `>` redirect to send the output to a file named `hello.txt` instead.
Notice that we don't get the text output to the terminal this time.

The `cat` command reads the file and sends the output to `stdout` - again we see the text on the terminal.

If you run the command with the redirect a few more times you will see that there is still only one line of text in the file.
This is because `>` overwrites the file.
If you want to append to the end of the file instead you can you the `>>` redirect instead.

The `cat` command can also accept data from `stdin` - this will happen if we don't provide a file argument:

```bash
kea-dev:~/tmp$ cat
Hello again!
Hello again!
^C
kea-dev:~/tmp$
```

The first line of `Hello again!` is typed into `stdin`, i.e. the terminal, by me, and `cat` then displays the output on `stdout` and we get the second line of `Hello again!`.
The `^C` comes from pressing `CONTROL` and `c` on the keyboard to stop sending data to the `cat` command.

We can also direct data into a command that reads data from `stdin` via the `<` redirect:

```bash
kea-dev:~/tmp$ cat < hello.txt
Hello world!
```

We can also directly send data between `stdout` from one command to `stdin` on another command - this is where *pipes* come into the picture.

If we list our current directory we get lots of information:

```bash
kea-dev:~/tmp$ ls -la
total 12
drwxr-sr-x    2 henrik   henrik        4096 Jan 26 12:58 .
drwxr-sr-x    9 henrik   henrik        4096 Jan 26 12:56 ..
-rw-r--r--    1 henrik   henrik          50 Jan 26 13:16 hello.txt
```

Say we are only interested in files, not directories, we can filter like this:

```bash
kea-dev:~/tmp$ ls -la | grep ^-
-rw-r--r--    1 henrik   henrik          50 Jan 26 13:16 hello.txt
```

First we run the `ls -la` command just like we did before.
The `|` is a *pipe* that sends `stdout` from the *left side* to `stdin` on the *right side*.
Then we use the `grep` command to filter the output: the search pattern `^-` means *the line must start with `-`*.
Alternatively, if we only wanted directories: `ls -la | grep ^d`.

These search patterns are written using *regular expressions*.
As a web developer you have probably come across them before.
You can find many resources on the Internet about regular expressions if you are interested.

We can also use simple strings as filters, but these are less powerful.

> Redirects and pipes may seem trivial or unnecessary, but if you learn how to combine commands they are extremely powerful.

As you probably realize by now, the CLI not only runs commands, it's a proper programming environment, and a very powerful one indeed.


## Installing Software

We will now take a very quick look at some fancy tools you can use in the CLI.
Some of these tools need to be installed by you, so we'll start by seeing how to install extra software on your development server.

### `apk`

We have already used the `apk` command to update the system, but `apk` can do more than that.

Let's get some help on how to use `apk`:

```bash
kea-dev:~/tmp$ apk
apk-tools 2.12.1, compiled for x86_64.

usage: apk [<OPTIONS>...] COMMAND [<ARGUMENTS>...]

Package installation and removal:
  add        Add packages to WORLD and commit changes
  del        Remove packages from WORLD and commit changes

System maintenance:
  fix        Fix, reinstall or upgrade packages without modifying WORLD
  update     Update repository indexes
  upgrade    Install upgrades available from repositories
  cache      Manage the local package cache

Querying package information:
  info       Give detailed information about packages or repositories
  list       List packages matching a pattern or other criteria
  dot        Render dependencies as graphviz graphs
  policy     Show repository policy for packages

Repository maintenance:
  index      Create repository index file from packages
  fetch      Download packages from global repositories to a local directory
  manifest   Show checksums of package contents
  verify     Verify package integrity and signature

Miscellaneous:
  audit      Audit system for changes
  stats      Show statistics about repositories and installations
  version    Compare package versions or perform tests on version strings

This apk has coffee making abilities.
For more information: man 8 apk
```

To get information on a package, in this case the `fd` command, we can use:

```bash
kea-dev:~/tmp$ apk info fd
fd-8.2.1-r0 description:
Simple, fast, user-friendly alternative to find

fd-8.2.1-r0 webpage:
https://github.com/sharkdp/fd

fd-8.2.1-r0 installed size:
1756 KiB

fd-8.1.1-r0 description:
Simple, fast, user-friendly alternative to find

fd-8.1.1-r0 webpage:
https://github.com/sharkdp/fd

fd-8.1.1-r0 installed size:
2364 KiB
```

The information includes short description, project repository, etc.

To install the package we use `apk add fd`.

> If you are a normal user, this will not work. You need extra privileges on the system to install software. We use the `sudo` command when we need to elevate our privileges, so the command above would be `sudo apk add fd` unless you are working as the `root` user, which you should avoid for security reasons.

|![sudo make me a sandwich](https://imgs.xkcd.com/comics/sandwich.png)|
|:--:|
|*Figure 2.4: `sudo` is a very powerful command. https://xkcd.com/149/*|

You can also find information about Alpine Linux packages at [https://pkgs.alpinelinux.org/packages](https://pkgs.alpinelinux.org/packages).

## Getting Help

!!! tip inline end

    You can use the `tldr` tool for easy exampls and explanations of shell commands.
    See [https://gitlab.com/-/snippets/2577706](https://gitlab.com/-/snippets/2577706)

As you can see above, we can sometimes get help about using a command by entering the command without parameters.
Unix-like systems has full documentation built in, however because Alpine Linux is a very small distribution, the so-called `man` pages are not always installed.
To be honest, the `man` pages is not the best place to find help, because you will usually get a *very* thorough explanation, and it can be a bit hard to find what you are looking for.


## Some Cool Tools

Here is a list of tools you can install on your machine to make the CLI even better:

- `fd`: powerful search (find) tool
- `fzf`: fuzzy-find
- `exa`: `ls` command with `git` integration
- `bat`: `cat` with syntax highlighting
- `diff-so-fancy`: a filter for highlighting `git` logs
- `entr`: run a command when files change
- `ngrok`: map an internal port to a public URL

And there are many more.
Use the `tldr` command to find out more information about these commands.


## Scripts And Functions

Scripts are used to automate work at the CLI.
Basically a script is just an executable file with a sequence of commands.

> It can sometimes be hard to figure out what a command will do - you can use [https://explainshell.com](https://explainshell.com) to get an explanation.

Let's make a script:

```bash
kea-dev:~/tmp$ touch sayhello

kea-dev:~/tmp$ ls -la
total 12
drwxr-sr-x    2 henrik   henrik        4096 Jan 27 17:48 .
drwxr-sr-x   10 henrik   henrik        4096 Jan 26 17:24 ..
-rw-r--r--    1 henrik   henrik          50 Jan 26 13:16 hello.txt
-rw-r--r--    1 henrik   henrik           0 Jan 27 17:48 sayhello

kea-dev:~/tmp$ chmod +x sayhello

kea-dev:~/tmp$ ls -la
total 12
drwxr-sr-x    2 henrik   henrik        4096 Jan 27 17:48 .
drwxr-sr-x   10 henrik   henrik        4096 Jan 26 17:24 ..
-rw-r--r--    1 henrik   henrik          50 Jan 26 13:16 hello.txt
-rwxr-xr-x    1 henrik   henrik           0 Jan 27 17:48 sayhello
```

First we `touch` the file `sayhello`.
Touching a file creates an empty file if the file does not exist already, whereas it updates the `last update` attribute if the file already exists.
Try touch the file again after a few minutes, list the directory, and you will see the time has been updated.

In the first listing above you will see the permissions reads `-rw-r--r--`.
The `henrik` user and group has read/write and read permissions, respectively, while others have read.

We then set the `x` flag using `chmod` (change mode), which changes the permissions to `-rwxr-x-r-x`.
The `x` indicates that the file is executable.

You will not be able to run the file as `sayhello`.
This is because Unix-like systems do not look for executable files outside their path.
To run the file from here, we need to tell the operating system where to find the file: `./sayhello`, which points to the current directory.

Running the file does nothing, because the file is empty, but you will not get an error.

Let's have the script do something - open the file for editing with the command `vim sayhello`.

In `vim` press `i` to enter *insert mode*, and type out the following:

```bash
#!/bin/sh

echo "Say hello"
```

To save the file press the `ESC` key, type `:w` and press enter.

The file has no file extension, so `vim` does not know how to syntax highlight the file.
To help `vim` determine the file type, press `ESC` and type `:set filetype=sh` and press enter.
You will now see the file highlighting in all its glory!

The first line of the file tells the shell that this script should be executed by the standard shell, in our case **zsh**, but it's better to allow the standard shell to run the script if we are not using special syntax that only works in a certain shell, which is not the case here.

The `echo` command just outputs the string, but that's not news to us.

Press `ESC` and type `:q` to leave `vim`.

If you now run the script using `./sayhello` you will see the script output the text as expected.
You have just written your first Unix compatible program.

Rather than creating a file for the script, we could create a function instead.
The file containing the function must be sourced before the shell can find the function, so one obvious place to put functions is in the `.zshrc`.

```bash
sayhello() {
    echo "Hello world function!"
}
```

Source the file using `source .zshrc` and run the script as so: `sayhello`.
You should see the output in your terminal.

You can also do `for` loops, both on the CLI and in scripts and functions:

```bash
  for

  Shell loop over parameters.

  - Perform a command with different arguments:
    for argument in 1 2 3; do command $argument; done

  - Perform a command in every directory:
    for d in *; do (cd $d; command); done
```

We also have `while` loops:

```bash
  while

  Simple shell loop.

  - Read stdin and perform an action on every line:
    while read line; do echo "$line"; done

  - Execute a command forever once every second:
    while :; do command; sleep 1; done
```

And finally `if` constructs:

```bash
  if

  Simple shell conditional.

  - Echo a different thing depending on a command's success:
    command && echo "success" || echo "failure"

  - Full if syntax:
    if condition; then echo "true"; else echo "false"; fi

  - List available if conditions:
    help test

  - Test if a given variable is empty:
    if [[ -z $GIT_BRANCH ]]; then echo "true"; else echo "false"; fi

  - Test if a file exists:
    if [[ -e filename ]]; then echo "true"; else echo "false"; fi

  - If directory not exists:
    if [[ ! -d path/to/directory ]]; then echo "true"; else echo "false"; fi
```

The `-z`, `-e`, and `-d` are short-hands of `test` - make sure you read the documentation for that command.

## Example Script - Compare Files In Two Directories

The example script we did together in class is listed below in its final version.

The script compares files by contents and lists out whether files pair-wise differ or are identical.
The directory structure used for testing is shown here:

```text
drwxr-sr-x   - henrik 11 Mar 17:03 ./
.rwxr-xr-x 378 henrik 10 Mar 14:24 ├── dubs*
.rw-r--r-- 580 henrik 10 Mar 13:51 ├── error.txt
drwxr-sr-x   - henrik 10 Mar 13:13 ├── one/
.rw-r--r--   0 henrik 10 Mar 13:12 │  ├── jhjhsdj
.rw-r--r--  13 henrik 10 Mar 13:13 │  └── test
drwxr-sr-x   - henrik 10 Mar 13:13 └── two/
.rw-r--r--   0 henrik 10 Mar 13:12    ├── dkjsdjkjkds
.rw-r--r--  13 henrik 10 Mar 13:13    └── xyz
```

And this is the script:

```bash
#!/bin/sh

if [ -z "$2" ]; then
    echo "Usage: dubs dir1 dir2"
    exit 1
fi

if [ ! -d "$1" ] || [ ! -d "$2" ]; then
    echo "Not a directory."
    exit 1
fi

for f1 in "$1"/*; do
    for f2 in "$2"/*; do
        if ! cmp "$f1" "$f2" 2> /dev/null; then
            echo "$f1 differs from $f2"
        else
            echo "$f1 is identical to $f2"
        fi
    done
done
```


## Example Script - `quif`

The script below is the solution to the `quif` assignment.
Template files are located in `~/.quif/`, and, as an example, running the command `quif index.html`, would copy the `html` template to the current folder.

The script could be put into the `~/.local/bin` folder for easy access.

```bash
#!/bin/sh

QUIFPATH="$HOME/.quif"

if [ -z "$1" ]; then
    printf "Please provide a file name.\n"
    exit 1
fi

FILENAME="$(basename "$1")"
EXTENSION="${FILENAME##*.}"

if [ -e "$QUIFPATH/$EXTENSION" ]; then
    cp "$QUIFPATH/$EXTENSION" "$(pwd)"/"$1"
else
    printf "No such file template.\n"
    exit 2
fi
```


## Writing To `stdout` And `stderr`

You can actively decide whether to send output to `stdout` or `stderr`.
Your scripts should send normal output to `stdout`, but all error messages should go to `stderr`.

Let's take a look at a small script file:

```bash
#!/usr/bin/env sh

echo "This is stdout."

echo "This is stderr." >&2
```

As you can see, by default output will go to `stdout`, and we can redirect output to `&2` for `stderr`.

You can handle these streams on the receiving side as well:

```bash
$ ./out-and-err

This is stdout.
This is stderr.
```

In the example above, both streams are attached to the terminal.

Now let's change that:

```bash
$ ./out-and-err 1>/dev/null
This is stderr.

$ ./out-and-err 2>/dev/null
This is stdout.
```

In the example above we first redirect `stdout` to the `null` device, and we can only see the `stderr` output, and vice versa in the second example.

The `null` device is just a pseudo file you can redirect anything to, and it will simply disappear.


## The Unix Philosophy

When we talk about Unix and Linux as Unix-like systems, and in a server context, we do not consider GUI systems.
As a web developer you will be working with these kinds of systems intensively, so knowing how to work with them will make you a better developer.

Notice how the commands we have looked at are all very simple.
This is because of the *Unix philosopy*: make tools that does just one simple thing, but does it well.
All these commands can be combined like LEGO bricks into powerful, generative tools.


## Textuality

Also note how most of the tools can not only work with files, but can also accept input via `stdin` and produce output to `stdout` and `stderr`.
When you create tools you should do the same.
Also, don't make strange formats and protocols - sending raw text is often a much better idea, because it can easily be processed by other tools.

## There Is No Place Like `~`

To help you get started configuring the shell and terminal to your taste, you can take a look at my GitLab snippets: [https://gitlab.com/dashboard/snippets](https://gitlab.com/dashboard/snippets).

You will find several configuration files here to make life in the shell a bliss.

![Master The Shell](../assets/master-the-shell.jpg){ width=75% }


## Assignments

> **Assignment 2.1:** Find information on the `lab` command. Build a script that will download all your GitLab repositories to a backup folder.

> **Assignment 2.2:** Write a script `quif`, that writes template files to the current directory. If given the command `quif index.html`, it should copy an `.html` template (say, from `~/.quif/templates/html`) and name it `index.html`. The user must be able to add new templates by simply putting a file in the template directory.

> Hint: look into `$1` and `$2` for command parameters, and `IFS='.'` to set a string split delimiter.

> **Assignment 2.3:** Create a script *snipet* that sources individual scripts from a folder `~/scripts/` for different hosts; say I have two machines `kea-dev` and `minime`, I would put two script files `kea-dev` and `minime` in the `~/scripts/` folder, and when the snipet is run on `kea-dev` it would source the `kea-dev` script (only), and likewise for `minime`. This would allow reusing your `.zshrc` file while still allow for differences between different machines and operating systems.

> **Assignment 2.4:** Create a script or function that 1) creates a directory with a given name, 2) runs `git init` in that directory, 3) creates a `README.md` file with some basic information about you, and 4) pushes the folder as a new project to GitLab. See `git push --set-upstream` for inspiration.

> **Assignment 2.5:** Take a look at [https://www.soimort.org/translate-shell/](https://www.soimort.org/translate-shell/). Based on this tool, try to make a flash card app, that displays a random word in one language, and then after a while shows the translation in your own language.

> **Assignment 2.6:** Create a *Guess the number game*. The user must guess a number between 1 and 100, and is told if the guess is too high or too low. Keep track of high-scores.
