# Lecture 5: Virtialization and Process Isolation - Part I

In this lecture we will look at virtualization techniques.


## Virtual Machines

You are already using a virtual machine, whether you know it or not.
Your `kea-dev` server is in fact a virtual machine.

Virtual machines solve a number of common problems that we deal with in IT:

- Allows us to run a different operating system than your host system
- Securely separates the entire operating system of the guest from the host
- Allows us to run several machines on a single host

A virtual machine has its own complete installation of the operating system.
This is a good thing in that there are very few dependencies between the host and the guest operating system, but it is also a very *heavy weight* solution, in that it can take a while to install, start the machine etc., and the resources needed are often quite high, especially for RAM and CPU.
It also means that you have to maintain the operating system of the virtual machine separately from the host, including hotfixes and updates.

Virtual machine software provides a *hypervisor* that sits between the hardware and the guest operating systems.
The hypervisor assigns resources to virtual machines, and mediates communication between hardware and the guest operating system (see figure 5.1).

There are many virtual machine solutions out there.
Some are meant for desktop virtualization:

- VMWare Workstation
- Parallels
- Virtual Box
- Vagrant (VM scripting)
- Windows Subsystem for Linux (WSL)

Some are meant for server virtualization:

- Xen
- KVM
- VMWare ESX
- QEMU

There's not a sharp line between the two categories, as some of the tools can be used for both purposes.

|![Difference between virtual machines and containers.](https://www.backblaze.com/blog/wp-content/uploads/2018/06/whats-the-diff-container-vs-vm.jpg)|
|:--:|
|*Figure 5.1: The difference between virtual machines and containers.*|


## Process Isolation (Containers)

*Process isolation* is a more light-weight approach than virtual machines.
Process isolation is a feature of the Linux kernel (cgroups), and some Unix kernels (e.g. *jails* in BSD).
Also, it makes use of `chroot` to separate the isolated process' filesystem from the host file system.

The best known implementation of process isolation on Linux is probably Docker.
Docker takes care of managing *images* and *containers*, as they are known in Docker terminology, but the process isolation itself is really done by the Linux kernel.


### `Dockerfile`, Images, and Containers

A `Dockerfile` describes how to build a Docker *image*, and a Docker image can be *instantiated* into a Docker *container*.

|![Dockerfile, image, and container.](https://miro.medium.com/max/1000/1*344Hiafp_XQXYVmuTOQzHA.png)|
|:--:|
|*Figure 5.2: `Dockerfile`, image, and container.*|

Let's try to make our own, minimal Docker container.
First, we need a `Dockerfile`:

```yaml title="Dockerfile"
FROM alpine:3.7
```

The `Dockerfile` only contains the `FROM` directive, that tells Docker which base image to use for our image, in this case `alpine:3.7`, i.e. Alpine Linux 3.7.
Alpine Linux is widely used in Docker because of its very small size, and very small system requirements.
That makes working with Docker very fast.

Create a file named `Dockerfile` with the one line above in an empty directory on the `kea-dev` server.

To build the Docker image (see figure 3.2) run this command from the same directory:

```bash
kea-dev:~/tmp$ docker build .

Sending build context to Docker daemon  2.048kB
Step 1/1 : FROM alpine:3.7
3.7: Pulling from library/alpine
5d20c808ce19: Pull complete
Digest: sha256:8421d9a84432575381bfabd248f1eb56f3aa21d9d7cd2511583c68c9b7511d10
Status: Downloaded newer image for alpine:3.7
 ---> 6d1ef012b567
Successfully built 6d1ef012b567
```

Docker images are built in *layers*.
Our very simple image only have one layer, the `5d20c808ce19` layer listed above.

Let's see a list of our images:

```bash

kea-dev:~/tmp$ docker image ls

REPOSITORY             TAG            IMAGE ID       CREATED         SIZE
alpine                 3.7            6d1ef012b567   23 months ago   4.21MB
```

!!! warning inline end

    You should always use up-to-date versions of software, unless you have some very special conditions, e.g., legacy software dependencies.

    We are only using this very old version her for demonstration purposes.

Even though we just built the image, we can see that it was created 23 months ago.
This is because Docker uses `digests` (see the build above) to determine whether images are changed or not.
Because we chose a very old version of Alpine Linux, and didn't add anything to it, we got this very old image.

Before we run a container based on the image, let's just get some data about our host system (the `kea-dev` server).
Run the command `uname -a` and make note of the output.
Also note that running the `ps` command will produce many lines of output.

Now let's run a container.
We set the `-it` flag to make the container interactive (otherwise it will just stop immediately), and run the `sh` command, which will give us a *shell* or CLI:

!!! note inline end

    Containers do *process isolation*: that means, when there is no process, there is no container.
    If the process of a container exits, the container will also exit.

```bash
kea-dev:~/tmp$ docker run -it alpine:3.7 sh
```

You will now get a `/ #` prompt, telling you that you are in the root directory (`/`), and that you are logged in as the `root` user (`#`).

If we run `uname -a` you will see a few differences:

```text
The host machine:
Linux kea-dev 5.10.5-0-virt #1-Alpine SMP Thu, 07 Jan 2021 15:05:37 UTC x86_64 Linux

The guest machine:
Linux 4b91cc5b17d6 5.10.5-0-virt #1-Alpine SMP Thu, 07 Jan 2021 15:05:37 UTC x86_64 Linux
```

Docker created a pseudo-random name for the guest machine, but even though the Docker image is 23 months old, it is still using a brand new kernel.
This is because the host machine provides the kernel for the guest machine.
The guest machine does not have its own kernel, nor a copy of the host machine's kernel, it is simply an isolated process with dependencies.

Let's make some more observations: `ls /home` does not contain your user's home directory, because docker use `chroot` to change the `/` root file system to its own isolated file system.

Also, running `ps` will produce very little output: just the shell process `sh` and the `ps` command that you just ran.
Compare this to the long list you had running the command on the host machine.

Another thing to notice:

!!! note inline end

    Container file systems are **not** persistent.

```bash
/ # cd root/
~ # ls
~ # touch hello
~ # ls
hello
~ # exit
kea-dev:~/tmp$ docker run -it alpine:3.7 sh
/ # cd root/
~ # ls
~ #
```

In the listing above we create a file `hello` in the `/root/` directory.
Then we exit the container and run it again.
Now, when we go to the `/root/` folder, the file is gone.

We created the image from an Alpine 3.7 base image.
The `3.7` part here is a *tag*.
Images like Alpine Linux are available in many versions, and if we want the latest one we can use the tag `latest`.
This can be a bit risky though.
If there are significant changes to a piece of software your image might just stop working all of a sudden.
For this reason you probably rather want to settle on the latest version, say `3.12`, and then be able to control any version changes yourself in the future.

You can find images for many well known pieces of software in [https://hub.docker.com](https://hub.docker.com), e.g. `node`, `mongo`, `postgres` etc.

Usually you need to add things to your image during the build process.
We can run commands using the `RUN` directive, so we can change the `Dockerfile` to:

```yaml title="Dockerfile"
FROM alpine:3.7

RUN apk update && apk upgrade
```

We can now rebuild the image:

```bash
kea-dev:~/tmp$ docker image build .

Sending build context to Docker daemon  2.048kB
Step 1/2 : FROM alpine:3.7
 ---> 6d1ef012b567
Step 2/2 : RUN apk update && apk upgrade
 ---> Running in 15ef2292ae26
fetch http://dl-cdn.alpinelinux.org/alpine/v3.7/main/x86_64/APKINDEX.tar.gz
fetch http://dl-cdn.alpinelinux.org/alpine/v3.7/community/x86_64/APKINDEX.tar.gz
v3.7.3-184-gffd32bfd09 [http://dl-cdn.alpinelinux.org/alpine/v3.7/main]
v3.7.3-183-gcc9ad2b48d [http://dl-cdn.alpinelinux.org/alpine/v3.7/community]
OK: 9054 distinct packages available
(1/2) Upgrading musl (1.1.18-r3 -> 1.1.18-r4)
(2/2) Upgrading musl-utils (1.1.18-r3 -> 1.1.18-r4)
Executing busybox-1.27.2-r11.trigger
OK: 4 MiB in 13 packages
Removing intermediate container 15ef2292ae26
 ---> 52431e692374
Successfully built 52431e692374
```

As you can see, the software update and upgrade was run during the build.

Listing the images show that we now have a new build based on the new layer we have added:

!!! note inline end

    Havinging to refer to images by their ID, e.g., `52431e692374`, is not very nice.
    You can use the `-t` tag like `-t myimage` to set a more memorable image name.

```bash
kea-dev:~/tmp$ docker image ls
REPOSITORY                       TAG            IMAGE ID       CREATED         SIZE
<none>                           <none>         52431e692374   2 minutes ago   6.11MB
alpine                           3.7            6d1ef012b567   23 months ago   4.21MB
```

We did not provide a name when we built the image, but we can still run a container using the image by specifying the `IMAGE ID`:

```bash
kea-dev:~/tmp$ docker run -it 52431e692374 sh
/ #
```

We will not go through all the directives here, but here is a quick list:

- `FROM`        : specify a base image
- `CMD`         : a command to run when the container is deployed
- `RUN`         : command to run during build
- `EXPOSE`      : expose a network port to the Docker network
- `COPY`        : copy files from host machine to image
- `ADD`       : copy or download assets to the image
- `ENTRYPOINT`  : make the container run a command as an executable
- `WORKDIR`     : specify working directory
- `ENV`         : specify environment variables

Also, make sure to check out the documentation: [https://docs.docker.com/engine/reference/builder/](https://docs.docker.com/engine/reference/builder/).


### Layers

You often need to install extra software into your container.
Let's install a few tools:

```yaml title="Dockerfile"
FROM alpine:3.12

RUN apk update
RUN apk upgrade
RUN apk add vim
RUN apk add exa
```

You may wonder why we have several `RUN` commands rather than combining into `apk update && apk upgrade` and `apk install vim exa`.
In fact, what we have done here could potentially cause problems because the `apk update` would be unchanged between builds.
If we later added a new package, we might install an old version of the package.

Instead, we would use:

!!! note inline end

    The layers are supposed to be *reusable*.
    Layers are cached and reused if they have not changed.
    However, if preceding layers have changed, a layer must update.
    Layers provide opportunity for optimization, but we will focus on making things work in this course.

```yaml title="Dockerfile"
RUN apk update &&\
    apk upgrade &&\
    apk add vim exa
```

But, other than this example, you should isolate layers and only copy necessary files.

Make sure not to copy dependencies such as virtual environments etc. into the Docker container, but make the build process install them instead.

For more information see [https://docs.docker.com/develop/develop-images/dockerfile_best-practices/](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/).


### Other Linux Distributions

Let's try to build a container with another Linux distribution, in this case Ubuntu:

!!! note inline end

    While in many ways identiacal in functionality, Alpine Linux and Ubuntu only share the kernel; everything else is implemented in different ways, including the package manager.
    In Ubuntu, you must use the `apt-get` package manager instead of `apk`.

```yaml title="Dockerfile"
FROM ubuntu:20.10

RUN apt-get update && apt-get install -y \
    aufs-tools \
    automake \
    build-essential \
    curl \
    dpkg-sig \
    libcap-dev \
    libsqlite3-dev \
    mercurial \
    reprepro \
    ruby1.9.1 \
    ruby1.9.1-dev \
    s3cmd=1.1.* \
    && rm -rf /var/lib/apt/lists/*
```

Try to build the container - compare how long time this takes compared to an Alpine Linux installation.


## Stepping Into A Running Container

We often need to see what happens inside a running container.
We can do that using the `exec` command.

Let us first start up a new container running the Redis database:

```bash
docker run -p 6379:6379 --name redis redis
```

We are doing a port mapping of `6379` from the host computer (left side of `:`), to `6379` inside the container (right side of `:`).

Also, we assign the name `redis` to the container, so we can more easily work with it.

You should see a message ending in `Ready to accept connections`.

Now let us try to connect to the conatiner:

!!! note inline end

    The `redis` in this command refers to the *container* name we assigned with `--name` in the previous command, not the *image* name.

```bash
docker exec -it redis sh
```

Because we chose a sensible name when we started the container, we can easily refer to it.

We are now met with a `#` root prompt, and we can run the Redis Command Line Interface command:

```bash
redis-cli
```

We can try to set and get a key in the Redis database just to see things are working:

```text
127.0.0.1:6379> set name henrik
OK
127.0.0.1:6379> get name
"henrik"
127.0.0.1:6379> exit
# exit
```

The first `exit` command exits the Redis CLI, and the second exits the `docker exec` mode and brings us back to the host machine.


## Not Just Terminal Apps

While everything in Docker is text-based, it is possible to encapsulate GUI applications also.
This is because most Unix-like systems runs graphics via a socket using X11.
As we know, a socket is just a file on the file system, so we can access these sockets.

Unfortunately, Windows does not support X11, but third party solutions are available.
The same is the case for macOS; even though macOS is indeed a Unix system, and used to support X11, this is no longer the case.
But again, third party solutions are available.

For more information, see [https://medium.com/better-programming/running-desktop-apps-in-docker-43a70a5265c4](https://medium.com/better-programming/running-desktop-apps-in-docker-43a70a5265c4).


## Assignments

### Assignment 5.1

Follow the steps below.

```bash
$ ssh kea-dev


  __  __  ______  ____        _____   ______  __    _
 |  |/ / |   ___||    \  ___ |     \ |   ___|\  \  //
 |     \ |   ___||     \|___||      \|   ___| \  \//
 |__|\__\|______||__|\__\    |______/|______|  \__/


kea-dev:~$ mkdir docker101/
kea-dev:~$ cd docker101/
kea-dev:~/docker101$ ls
kea-dev:~/docker101$ docker run -dp 8000:80 docker/getting-started
```

- Explain what is going on - use documentation as needed.
- Run the steps from above - access `http://127.0.0.1:8000` from your browser, follow the tutorial sections:
    - Getting Started
    - Our Application
    - Updating our App
    - Sharing our App


### Assignment 5.2

!!! note inline end

    This assignment is difficult, and not mandatory.
    It is, however, very interesting!

Try to run some of the GUI applications from the link in *Not Just Terminal App* on your local computer (if you can run Docker on your machine).

As an extra teaser: do you think you can relay the connection from a GUI Docker container running on `kea-dev` to your local machine?
This would probably be very slow, but still interesting to see if it is possible.
