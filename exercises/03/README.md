Docker client and Docker engine use an API
==========================================

Overview
--------

* Docker engine is a daemon to build and run containers.
* Docker client is a utility to interact with Docker engine via its API.


Versions
--------

The versions of the Docker client and engine do *not* have to match, but<br/>
the client and engine *must* use the same API version.


Local vs Remote API
-------------------

1. Log into your CoreOS host and check the version:

    ```
    core@ip-192-168-254-162 ~ $ docker version
    Client:
     Version:      1.8.3
     API version:  1.20
     Go version:   go1.5.1
     Git commit:   cedd534-dirty
     Built:        Sat Oct 24 19:42:59 UTC 2015
     OS/Arch:      linux/amd64

    Server:
     Version:      1.8.3
     API version:  1.20
     Go version:   go1.5.1
     Git commit:   cedd534-dirty
     Built:        Sat Oct 24 19:42:59 UTC 2015
     OS/Arch:      linux/amd64
    ```

1. On your workstation, update `~/.bashrc`:

    ```
    export DOCKER_HOST='tcp://192.168.254.162:2375'
    ```

   Then source it:

    ```
    user@devenv:~$ source .bashrc 

    user@devenv:~$ echo ${DOCKER_HOST}
    tcp://192.168.254.162:2375
    ```

1. On your workstation, check the version
   to confirm both client and server have same API version:

    ```
    user@devenv:~$ docker version
    Client:
     Version:      1.8.2
     API version:  1.20
     Go version:   go1.4.2
     Git commit:   0a8c2e3
     Built:        Thu Sep 10 19:10:10 UTC 2015
     OS/Arch:      linux/amd64

    Server:
     Version:      1.8.3
     API version:  1.20
     Go version:   go1.5.1
     Git commit:   cedd534-dirty
     Built:        Sat Oct 24 19:42:59 UTC 2015
     OS/Arch:      linux/amd64
    ```
