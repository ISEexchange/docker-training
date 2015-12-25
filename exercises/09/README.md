Build an image from source code
===============================


Builder image
-------------

1. *Make* a local directory:

    ```
    core@host:~$ mkdir -p ${HANDLE}_hello/
    core@host:~$ cd ${HANDLE}_hello/
    ```

1. *Edit* `hello.c` that contains:

    ```
    #include<stdio.h>

    main()
    {
        printf("Hello World\n");
    }
    ```

1. *Edit* `Dockerfile.build` that contains:

    ```
    FROM alpine:3.3

    # Work around ISE network access.
    ENV http_proxy=http://proxy2.inf.ise.com:3128
    ENV https_proxy=http://proxy2.inf.ise.com:3128

    RUN apk add --update alpine-sdk && \
        rm -f /var/cache/apk/*

    COPY . /src

    RUN cd /src && \
        gcc -o hello -static hello.c

    ```

1. *Build* an image:

    ```
    core@host:~$ docker build --rm -t ${HANDLE}/hello_builder -f Dockerfile.build .
    ```

1. *Create* a stopped container:

    ```
    core@host:~$ docker create --name ${HANDLE}_hello_builder ${HANDLE}/hello_builder true
    ```

1. *Copy* the built binary out of the container:

    ```
    core@host:~$ docker cp ${HANDLE}_hello_builder:/src/hello .
    ```


Runtime image
-------------

1. *Edit* `Dockerfile.runtime` that contains:

    ```
    FROM scratch

    COPY hello /hello

    ENTRYPOINT ["/hello"]
    ```

1. *Build* the runtime image:

    ```
    core@host:~$ docker build --rm -t ${HANDLE}/hello -f Dockerfile.runtime .
    ```

1. *Run* the app:

    ```
    core@host:~$ docker run --rm -it ${HANDLE}/hello
    Hello World
    ```


Compare image sizes
-------------------

Compare the image sizes of your two hello images:

```
core@host:~$ docker images | grep ${HANDLE}/hello
```
