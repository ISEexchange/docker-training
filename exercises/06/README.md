Container basics
================


1. Export environment variables. We want to name our containers,
   and an environment variable helps us do that in a lab.

    ```
    # Use your own github handle here!
    user@devenv:~$ export HANDLE=jumanjiman

    # Use an appropriate IP address here!
    user@devenv:~$ export DOCKER_HOST=tcp://192.168.254.162:2375
    ```

1. *Run* your first container and look around:

    ```
    user@devenv:~$ docker run -it --name ${HANDLE}_first alpine:3.3 sh
    / # du -lshx /
    328.0K  /

    / # touch blah

    / # ls -l blah
    -rw-r--r--    1 root     root             0 Nov  2 20:34 blah

    / # id
    uid=0(root) gid=0(root) groups=0(root),1(bin),2(daemon),3(sys),4(adm),6(disk),10(wheel),11(floppy),20(dialout),26(tape),27(video)

    / # ps -ef
    PID   USER     TIME   COMMAND
        1 root       0:00 sh
        7 root       0:00 ps -ef

    / # exit
    ```

1. *Run* a second container, this time with a *read-only* root filesystem:

    ```
    user@devenv:~$ docker run -it --read-only --name ${HANDLE}_second alpine:3.3 sh
    / # touch blah
    touch: blah: Read-only file system

    / # exit
    ```

1. *Run* a third container, this time in *detached* mode:

    ```
    user@devenv:~$ docker run -d --read-only --name ${HANDLE}_third alpine:3.3 sh -c 'while true; do echo blah; sleep 1; done'
    f84cb8dbac4e50822cd19b424f3c8a5c9b1a0501e89ee27b80c3f3dced7556ae
    ```

1. View *running* containers:

    ```
    user@devenv:~$ docker ps
    CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
    f84cb8dbac4e        alpine:3.3          "sh -c 'while true; d"   51 seconds ago      Up 51 seconds                           jumanjiman_third
    ```

1. View the *logs* of your running container:

    ```
    user@devenv:~$ docker logs -f ${HANDLE}_third
    blah
    blah
    blah
    blah
    blah
    ^C
    ```

1. View the *top* processes in a running container:

    ```
    user@devenv:~$ docker top ${HANDLE}_third
    UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
    root                14774               7410                0                   01:04               ?                   00:00:00            sh -c while true; do echo blah; sleep 1; done
    root                14788               14774               0                   01:05               ?                   00:00:00            sleep 1
    ```

1. Enter a *running* container:

    ```
    user@devenv:~$ docker exec -it ${HANDLE}_third sh
    / # cat /etc/os-release 
    NAME="Alpine Linux"
    ID=alpine
    VERSION_ID=3.2.0
    PRETTY_NAME="Alpine Linux v3.2"
    HOME_URL="http://alpinelinux.org"
    BUG_REPORT_URL="http://bugs.alpinelinux.org"
    ```

1. Compare to `/etc/os-release` of Fedora:

    ```
    user@devenv:~$ docker run -it --name ${HANDLE}_fourth fedora:21 cat /etc/os-release
    NAME=Fedora
    VERSION="21 (Twenty One)"
    ID=fedora
    VERSION_ID=21
    PRETTY_NAME="Fedora 21 (Twenty One)"
    ANSI_COLOR="0;34"
    CPE_NAME="cpe:/o:fedoraproject:fedora:21"
    HOME_URL="https://fedoraproject.org/"
    BUG_REPORT_URL="https://bugzilla.redhat.com/"
    REDHAT_BUGZILLA_PRODUCT="Fedora"
    REDHAT_BUGZILLA_PRODUCT_VERSION=21
    REDHAT_SUPPORT_PRODUCT="Fedora"
    REDHAT_SUPPORT_PRODUCT_VERSION=21
    ```

1. View *all* containers:

    ```
    user@devenv:~$ docker ps -a
    CONTAINER ID        IMAGE               COMMAND                  CREATED              STATUS                     PORTS               NAMES
    f84cb8dbac4e        alpine:3.3          "sh -c 'while true; d"   About a minute ago   Up About a minute                              jumanjiman_third
    d3b2ac044020        alpine:3.3          "sh"                     4 minutes ago        Exited (0) 4 minutes ago                       jumanjiman_second
    83e7a817612e        alpine:3.3          "sh"                     4 minutes ago        Exited (0) 4 minutes ago                       jumanjiman_first
    ```

1. View *stopped* containers:

    ```
    user@devenv:~$ docker ps -f status=exited
    CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                     PORTS               NAMES
    d3b2ac044020        alpine:3.3          "sh"                4 minutes ago       Exited (0) 4 minutes ago                       jumanjiman_second
    83e7a817612e        alpine:3.3          "sh"                5 minutes ago       Exited (0) 4 minutes ago                       jumanjiman_first
    ```

1. *Stop* your running container:

    ```
    user@devenv:~$ docker stop ${HANDLE}_third
    jumanjiman_third
    ```

1. *Inspect* a container (either running or not):

    ```
    user@devenv:~$ docker inspect ${HANDLE}_third
    -snip lots of output-
    ```

1. List *all* your containers:

    ```
    user@devenv:~$ docker ps -af name=${HANDLE}*
    CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                       PORTS               NAMES
    11bbdecff85f        alpine:3.3          "sh -c 'while true; d"   5 minutes ago       Exited (137) 5 minutes ago                       jumanjiman_third
    d3b2ac044020        alpine:3.3          "sh"                     14 minutes ago      Exited (0) 14 minutes ago                        jumanjiman_second
    83e7a817612e        alpine:3.3          "sh"                     15 minutes ago      Exited (0) 15 minutes ago                        jumanjiman_first
    ```

1. Do the same thing *quietly*:

    ```
    user@devenv:~$ docker ps -aqf name=${HANDLE}*
    11bbdecff85f
    d3b2ac044020
    83e7a817612e
    ```

1. *Remove* all your containers:

    ```
    user@devenv:~$ docker ps -aqf name=${HANDLE}* | xargs docker rm
    11bbdecff85f
    d3b2ac044020
    83e7a817612e
    ```

1. For one-off containers, *remove* the container on exit:

    ```
    user@devenv:~$ docker run --rm -it --name ${HANDLE}_rm_on_exit alpine:3.3 cat /etc/os-release
    NAME="Alpine Linux"
    ID=alpine
    VERSION_ID=3.2.0
    PRETTY_NAME="Alpine Linux v3.2"
    HOME_URL="http://alpinelinux.org"
    BUG_REPORT_URL="http://bugs.alpinelinux.org"

    user@devenv:~$ docker ps -af name=${HANDLE}*
    CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
    ```
