
# Docker for WSL

The following script creates matching function names for docker for Linux and Docker for Windows. It sets up the correct paths for mounting shares, giving you a pretty close experience of using Docker for Linux in WSL.

```bash
curl -s \
    https://raw.githubusercontent.com/leohinojosa/wsldocker/master/bash_docker > .bash_docker && \
    chmod 666 .bash_docker && \
    echo . ~/.bash_docker >> .bashrc && \
    source ~/.bashrc
```

## Pre-reqs
1. It only works in Windows Creators Update.
1. Install docker engine in Windows
2. Install the Windows Subsystem for Linux,
    [What's new in Bash/WSL](https://blogs.msdn.microsoft.com/commandline/2017/04/11/windows-10-creators-update-whats-new-in-bashwsl-windows-console/)
3. Install docker client in bash
4. (Optional) Install docker-compose in bash

## Setup ##
1.In the WSL Terminal, run the following script to download the bash_docker aliases and source them to your .bashrc file:

```bash
curl -s \
    https://raw.githubusercontent.com/leohinojosa/wsldocker/master/bash_docker > .bash_docker && \
    chmod 666 .bash_docker && \
    echo . ~/.bash_docker >> .bashrc && \
    source ~/.bashrc
```

2. Run ``docker ps``

3. Optionally link your Windows Home directory to your Bash Home Directory `ln -s /c/Users/my-user/projects ~/projects`


## Usage
Everytime you open a WSL terminal it will try to create the `/c` mount point. This will ask for your WSL password. Subsequent WSL terminals will see that its already mounted and it won't ask for your password again.

By default, the script will eval your `docker-machine env` that way it will be available everytime you open a new WSL terminal.

If you need to create a new docker-machine, the Windows command is aliased and is able to run the native docker-machine command so commands like `docker-machine create -d virtualbox testmachine` or `docker-machine ls` are routed to the Windows exe.

Specifically `docker-machine env` parses the windows paths and translates them to valid a unix paths, so `export DOCKER_CERT_PATH="C:\Users\MyUser\.docker\machine\machines\default"` gets translated to `export DOCKER_CERT_PATH="/c/Users/MyUser/.docker/machine/machines/default"`. This way you can run `eval $(docker machine env mycustomvm)`.

The rest of the docker-machine commands get executed normally

## Notes
When you install Docker, it creates a VM that is running the docker-engine. This VM has a default shared mount in the `C:\Users` windows folder in the `/c/Users` mount point.

When WSL runs it has a default mount point for the `C:` drive at `/mnt/c`

So for the Docker Engine to be able to match the same shared location with WSL, we need to create a mountpoint from `/mnt/c` to `/c`.

```$ sudo mount -o bind /mnt/c /c```

Once we run this, the docker-engine will be able to match the paths and mount the host files in any container. 

Neither the Docker VM nor WSL store changes for this mountpoint, so if we create a new mount point it will be reset everytime you run `docker-machine start` or open a WSL Terminal.

**If you decide to use a symbolic link instead of a mount point your docker client will use /mnt/c path instead of /c/ so it won't match the pre-mapped path in the VM and your files won't be mounted in docker containers**


