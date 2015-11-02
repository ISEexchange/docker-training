Docker images
=============


Namespaces
----------

Observe that every image name has four parts:

1. `quay.io`: name of registry (defaults to Docker Hub if empty)
1. `iseexchange`: user or organization (defaults to official library if empty)
1. `t7`: name of image
1. `10.2.40-20151029`: specific tag

Full example:

    quay.io/iseexchange/t7:10.2.40-20151029

Shorter example:

    jumanjiman/puppet:latest

Shortest example:

    alpine:3.2


Visualization
-------------

1. Visit https://imagelayers.io/ and view one or two images.
1. Visit https://quay.io/repository/iseexchange/t7?tab=tags and visualization a tag.


CLI
---

1. Pull a tagged image that you saw on Docker Hub. Examples:

    ```
    user@devenv:~$ docker pull alpine:3.2
    user@devenv:~$ docker pull fedora:21
    user@devenv:~$ docker pull jumanjiman/caddy:latest
    ```

1. View all images that have already been pulled:

    ```
    user@devenv:~$ docker images
    REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
    alpine              3.2                 31f630c65071        4 months ago        5.25 MB
    fedora              21                  e26efd418c48        5 months ago        241.3 MB
    ```

1. View "dangling" images:

    ```
    user@devenv:~$ docker images -f dangling=true
    REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
    <none>              <none>              1e51e584b8b0        3 days ago          11 MB
    <none>              <none>              77030a99f55e        3 days ago          11 MB
    <none>              <none>              bba0c953f77e        3 days ago          11 MB
    ```

1. Remove image (`rmi`):

    ```
    user@devenv:~$ docker rmi 1e51e584b8b0
    Deleted: 1e51e584b8b0c7bec5a764d088706b535acbf0ff243e2560f0f0f24a1c13009b

    user@devenv:~$ docker images -f dangling=true
    REPOSITORY          TAG                 IMAGE ID            CREATED             VIRTUAL SIZE
    <none>              <none>              77030a99f55e        3 days ago          11 MB
    <none>              <none>              bba0c953f77e        3 days ago          11 MB
    ```
