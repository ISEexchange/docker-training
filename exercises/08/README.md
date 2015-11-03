Build an image
==============


Fedora Apache Server
--------------------

1. *Export* environment variables. We want to name our containers,
   and an environment variable helps us do that in a lab.

    ```
    # Use your own github handle here!
    core@host:~$ export HANDLE=jumanjiman

    # Use an appropriate IP address here!
    user@devenv:~$ export DOCKER_HOST=tcp://192.168.254.162:2375

    # Each person gets a DIFFERENT port.
    user@devenv:~$ export MY_PORT=1024
    ```

1. *Make* a local directory:

    ```
    core@host:~$ mkdir -p ${HANDLE}_apache/fedora/
    core@host:~$ cd ${HANDLE}_apache/fedora/
    ```

1. *Edit* a Dockerfile that contains:

    ```
    FROM fedora:21

    ENV packages="httpd net-tools hostname"

    # Work around ISE network access.
    ENV http_proxy=http://proxy2.inf.ise.com:3128
    ENV https_proxy=http://proxy2.inf.ise.com:3128

    # Attempt to work around
    # https://bugzilla.redhat.com/show_bug.cgi?id=1213602
    RUN yum -y install ${packages} || \
        yum -y install ${packages} && \
        yum clean all

    EXPOSE 80/tcp

    ENTRYPOINT ["/usr/sbin/httpd"]
    CMD ["-D", "FOREGROUND"]

    ```

1. *Build* an image:

    ```
    core@host:~$ docker build --rm -t ${HANDLE}/apache:fedora .
    ```

1. *Run* a container and expose ports:

    ```
    core@host:~$ docker run -d -p ${MY_PORT}:80 -h ${HANDLE}.example.com --name ${HANDLE}_apache ${HANDLE}/apache:fedora
    ```

1. Confirm the site is reachable:

    ```
    core@host:~$ curl http://<ip-of-host>:${MY_PORT}
    ```

1. Enter the container and inspect ports plus hostname:

    ```
    core@host:~$ docker exec -it ${HANDLE}_apache sh
    sh-4.3# netstat -tulpn
    Active Internet connections (only servers)
    Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
    tcp6       0      0 :::80                   :::*                    LISTEN      1/httpd             

    sh-4.3# hostname -f
    jumanjiman.example.com

    sh-4.3# exit
    ```

1. Remove the container:

    ```
    core@host:~$ docker rm -f ${HANDLE}_apache
    ```


Alpine Apache Server
--------------------

1. *Make* a local directory:

    ```
    core@host:~$ mkdir -p ${HANDLE}_apache/alpine/
    core@host:~$ cd ${HANDLE}_apache/alpine/
    ```

1. *Edit* a Dockerfile that contains:

    ```
    FROM alpine:3.2

    ENV packages="apache2"

    # Work around ISE network access.
    ENV http_proxy=http://proxy2.inf.ise.com:3128
    ENV https_proxy=http://proxy2.inf.ise.com:3128

    RUN apk add --update ${packages} && \
        rm -f /var/cache/apk/*

    EXPOSE 80/tcp

    ENTRYPOINT ["/usr/sbin/httpd"]
    CMD ["-D", "FOREGROUND"]
    ```

1. *Build* an image:

    ```
    core@host:~$ docker build --rm -t ${HANDLE}/apache:alpine .
    ```

1. *Run* a container and expose ports:

    ```
    core@host:~$ docker run -d -p ${MY_PORT}:80 -h ${HANDLE}.example.com --name ${HANDLE}_apache ${HANDLE}/apache:alpine
    ```

1. Confirm the site is reachable:

    ```
    core@host:~$ curl http://<ip-of-host>:${MY_PORT}
    ```

1. Enter the container and inspect ports:

    ```
    core@host:~$ docker exec -it ${HANDLE}_apache sh
    / # netstat -tulpn
    Active Internet connections (only servers)
    Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
    tcp6       0      0 :::80                   :::*                    LISTEN      1/httpd             

    / # hostname -f
    jumanjiman.example.com

    / # exit
    ```

1. Remove the container:

    ```
    core@host:~$ docker rm -f ${HANDLE}_apache
    ```


Compare image sizes
-------------------

Compare the image sizes of your two apache images:

```
core@host:~$ docker images | grep ${HANDLE}/apache
```
