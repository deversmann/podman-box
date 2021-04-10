# podman-box

## Description

"Containers are Linux!" So how does Docker run on a Mac?  Well, the dirty little secret is: while you aren't looking, it spins up a VM in the background running Linux and hosting your containers.

This project aims to make it easy to do the same, but using Podman (and other open-source tools) instead.

## Requirements
- [Vagrant](https://www.vagrantup.com)
- [VirtualBox](https://www.virtualbox.org)
- [Podman](https://podman.io)

All three can be installed using Homebrew:
```bash
brew cask install vagrent virtualbox
brew install podman 
```

From the project directory, start the VM
```bash
vagrant up
```

After the VM is running the issue following commands to configure your terminal session to use the remote:

```bash
export CONTAINER_SSHKEY=$PWD/.vagrant/machines/default/virtualbox/private_key
export CONTAINER_HOST=ssh://vagrant@127.0.0.1:2222/run/user/1000/podman/podman.sock
podman system connection add vagrant --identity $CONTAINER_SSHKEY $CONTAINER_HOST
```
> :warning: The port in the code snip above assumes that you have no other vagrant VMs running, but the `vagrant up` command will output the right version of the above.

If this is the only connection you've added to podman, it will be the default and you can use it directly in all `podman` commands.  If you've added other connections, then you know how to select this one.

## Pros and Cons


| Pros | Cons |
|--|--|
| Easy to get container workflows going from the mac terminal | Doesn't handle forwarding ports back to the mac |


### Notes to not forget
#### (Things not readily obvious that took forever to figure out the first time... and maybe the second too)
- An error like this, `Error: cannot connect to the Podman socket, please verify that Podman REST API service is running: server API version is too old. Client "3.0.0" server "2.0.0"` means that there is an incompatible version mismatch between the "local" podman and the "remote" podman. Podman seems to like to frequently completely break compatibility between versions.
- The `enable-linger` directive in the playbook is really important!  It's what allows the user-defined socket to remain available when the user is not logged in.
- Some pretty decent info lives here: https://github.com/containers/podman/blob/master/docs/tutorials/mac_win_client.md
