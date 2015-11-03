Volume basics
=============


Host volumes
------------

1. *Export* environment variables. We want to name our containers,
   and an environment variable helps us do that in a lab.

    ```
    # Use your own github handle here!
    core@host:~$ export HANDLE=jumanjiman

    # Use an appropriate IP address here!
    user@devenv:~$ export DOCKER_HOST=tcp://192.168.254.162:2375
    ```

1. *Make* a local config file:

    ```
    core@host:~$ mkdir ${HANDLE}_config/

    core@host:~$ echo "me=${HANDLE}" > ${HANDLE}_config/github.conf
    ```

1. *Make* a local data directory:

    ```
    core@host:~$ mkdir ${HANDLE}_data/

    ```

1. *Run* a container with volume mounts:

    ```
    core@host:~$ docker run --rm -it --read-only --name ${HANDLE}_volumes -v $(pwd)/${HANDLE}_config:/configs:ro -v $(pwd)/${HANDLE}_data:/data:rw alpine:3.2 sh
    / # cat /configs/github.conf 
    me=jumanjiman

    / # source /configs/github.conf 

    / # echo ${me}
    jumanjiman

    / # echo ${me} > /data/writable.dat

    / # cat /data/writable.dat 
    jumanjiman

    / # echo me=somebody_else > /configs/github.conf 
    sh: can't create /configs/github.conf: Read-only file system

    / # exit
    ```


Data containers
---------------

1. *Create* a simple data container:

    ```
    core@host:~$ docker create -v /data --name ${HANDLE}_data alpine:3.2 true
    -snip-

    core@host:~$ docker ps -af name=${HANDLE}_data
    -snip-

    core@host:~$ docker inspect ${HANDLE}_data
    -snip-
    ```

1. Look inside the data container:

    ```
    core@host:~$ docker run --rm -it --volumes-from ${HANDLE}_data alpine:3.2 ls /data
    ```

1. Add a file to the data container:

    ```
    core@host:~$ docker run --rm -it --volumes-from ${HANDLE}_data alpine:3.2 sh
    / # echo foo > /data/bar
    / # exit
    ```

1. Look inside the data container:

    ```
    core@host:~$ docker run --rm -it --volumes-from ${HANDLE}_data alpine:3.2 ls /data
    /data/bar

    core@host:~$ docker run --rm -it --volumes-from ${HANDLE}_data alpine:3.2 cat /data/bar
    foo
    ```

1. Look inside the data container from a different OS:

    ```
    core@host:~$ docker run --rm -it --volumes-from ${HANDLE}_data fedora:21 cat /data/bar
    foo
    ```


Copy from a container
---------------------

1. *Copy* a file from a data container into the host:

    ```
    core@host:~$ mkdir /tmp/${HANDLE}

    core@host:~$ docker cp ${HANDLE}_data:/data /tmp/${HANDLE}
    ```

1. *Remove* the data container. You know the command!
