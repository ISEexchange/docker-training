Prepare CoreOS host
===================


Overview
--------

Populate `/home/core/.ssh/authorized_keys` so you can login remotely.


Steps
-----

1.  OpenSSH-7.1 does not accept DSA keys of any size or RSA keys 1024 bits and smaller.
    Confirm that your ssh key pair is 2048 bits or larger and uses RSA (not DSA).

    ```
    user@devenv:~$ for key in .ssh/id*; do
    > ssh-keygen -l -f ${key}
    > done
    2048 19:88:40:4e:f2:ab:c3:fb:fb:07:ca:0a:c2:d7:0e:05  pmorgan@ise.com (RSA)
    2048 19:88:40:4e:f2:ab:c3:fb:fb:07:ca:0a:c2:d7:0e:05  pmorgan@ise.com (RSA)
    2048 f6:47:b8:2c:b3:0e:4f:87:f7:44:46:17:e9:2b:c0:cf  pmorgan@github (RSA)
    2048 f6:47:b8:2c:b3:0e:4f:87:f7:44:46:17:e9:2b:c0:cf  pmorgan@github (RSA)
    ```

1. Publish your pubkey(s) to https://github.com/settings/ssh

   - You control your key pairs
   - Sysadmins can easily fetch your pubkeys

1. Somebody who already has access to CoreOS host adds your pubkey to the host.

    ```
    core@host ~ $ handle=belldon

    core@host ~ $ curl -L -o .ssh/authorized_keys.d/${handle} https://github.com/${handle}.keys
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100   381    0   381    0     0   1258      0 --:--:-- --:--:-- --:--:--  1300

    core@host ~ $ update-ssh-keys 
    Updated /home/core/.ssh/authorized_keys
    ```

1. On your workstation, update `~/.ssh/config` to include:

    ```
    # Replace this IP with the hostname or IP of _your_ CoreOS host!
    host 192.168.254.162
      User core
      IdentityFile ~/.ssh/id_rsa.github
      Port 22
    ```

1. Confirm your access:

    ```
    user@devenv:~$ ssh 192.168.254.162
    Last login: Sat Oct 31 18:18:12 2015 from 192.168.254.21
    CoreOS alpha (845.0.0)

    core@host ~ $ 
    ```
