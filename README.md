# Demo Vagrant

Demonstrate Vagrant for building machine images. This demo currently uses Vagrant with VirtualBox and Ubuntu.

Contents:

* [](#)


## Get VirtualBox

Go to https://www.virtualbox.org/

Example to get VirtualBox on macOS via brew:

```sh
$ brew cask install virtualbox
```

Verify:

```sh
$ vboxmanage --version
5.1.14r112924
```

## Get Vagrant

Go to https://www.vagrantup.com

Example to get Vagrant on macOS via brew:

```sh
$ brew cask install vagrant
$ brew cask install vagrant-manager
```

Verify:

```sh
$ vagrant --version
Vagrant 1.9.1


### Get Vagrant Manager (optional)

Vagrant Manager is a third-party tool for macOS to help manage your vagrant machines in one place. 

Go to http://vagrantmanager.com/

Example to get Vagrant on macOS via brew:

```sh
$ brew cask install vagrant-manager
```


## Create an image

Create a demo directory:

```sh
$ mkdir foo
$ cd foo
```

Fetch an image:

```sh
$ vagrant init hashicorp/precise64
A `Vagrantfile` has been placed in this directory. 
You are now ready to `vagrant up` your first virtual 
environment! Please read the comments in the Vagrantfile 
as well as documentation on `vagrantup.com` for more 
information on using Vagrant.
```

Bring it up:

```sh
$ vagrant up
Bringing machine 'default' up with 'virtualbox' provider...
==> default: Importing base box 'bento/ubuntu-16.10'...
...
```

If you want, you can fetch a more-recent box:

```sh
$ vagrant init bento/ubuntu-18.04
$ vagrant up --provider virtualbox
```

If you get this error:

```sh
default: SSH auth method: private key
Timed out while waiting for the machine to boot. This means that
Vagrant was unable to communicate with the guest machine within
the configured ("config.vm.boot_timeout" value) time period.
```

Then try:

  * Launch VirtualBox

  * Look for the image name that starts with the name of your directory and the word default, such as "foo_default_1234".

  * Look at the logs to see what went wrong.

  * Vagrant and/or Virtualbox sets up your workstations networking so that port 2222 is forwarded to port 22 of the virtual machine. If you explicitly add the configuration option sshd_port: 2222 to group_vars/development/main.yml, then SSHD is configured to answer on 192.168.50.5:2222 only, and the forwarder from 127.0.0.1:2222 to port 22 of the VM fails because SSH isn't listening on port 22 as expected.

  * Make sure you do not configure your local development to use port 2222, the VM should answer on port 22 and let the virtualbox network configuration do the port forwarding.

Troubleshooting SSH:

```sh
$ vagrant ssh-config
Host default
HostName 127.0.0.1
User vagrant
Port 2222
UserKnownHostsFile /dev/null
StrictHostKeyChecking no
PasswordAuthentication no
IdentityFile /Users/alice/.vagrant.d/insecure_private_key
IdentitiesOnly yes
LogLevel FATAL
```

```sh
$ vagrant ssh -- -vvv
OpenSSH_7.4p1, OpenSSL 1.0.2k  26 Jan 2017
debug1: Reading configuration data /Users/joel/.ssh/config
debug1: Reading configuration data /usr/local/etc/ssh/ssh_config
debug2: resolving "127.0.0.1" port 2222
debug2: ssh_connect_direct: needpriv 0
debug1: Connecting to 127.0.0.1 [127.0.0.1] port 2222.
debug1: Connection established.
debug1: key_load_public: No such file or directory
debug1: identity file /Users/joel/.vagrant.d/insecure_private_key type -1
debug1: key_load_public: No such file or directory
debug1: identity file /Users/joel/.vagrant.d/insecure_private_key-cert type -1
debug1: Enabling compatibility mode for protocol 2.0
debug1: Local version string SSH-2.0-OpenSSH_7.4
```

If you use version control, such as git, then you may want to commit the Vagrantfile:

```sh
$ git add Vagrantfile
$ git commit "Add Vagrantfile"
```

If you use AWS, then you may want to upload the box.

```sh
$ vagrant plugin install vagrant-aws
$ vagrant box add dummy https://github.com/mitchellh/vagrant-aws/raw/master/dummy.box
$ vagrant up --provider=aws
```
