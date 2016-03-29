# DCOS Training

You need a computer with Vagrant (https://www.vagrantup.com) and VirtualBox (https://www.virtualbox.org) installed. Please download the ubuntu box ahead of the training with the command `vagrant box add ubuntu/wily64`.

# Container management with Docker and Mesos/Marathon

## Contents

Initiate the vagrant environment `vagrant init ubuntu/wily64`. Increase the Memory for the vagrant box by adding the following lines to `Vagrantfile`:
```
config.vm.provider "virtualbox" do |vb|
  # Display the VirtualBox GUI when booting the machine
  vb.gui = false

  # Customize the amount of memory on the VM:
  vb.memory = "1024"
end
```

Start the box `vagrant up` and connect to it with `vagrant ssh`.

First of all we install the DCOS CLI: https://docs.mesosphere.com/administration/cli/install-cli/#linux

```
$ pwd
/home/vagrant
$ curl -O https://bootstrap.pypa.io/get-pip.py
$ sudo python get-pip.py
$ sudo pip install virtualenv
$ mkdir dcos && cd dcos
$ curl -O https://downloads.mesosphere.com/dcos-cli/install.sh
$ bash install.sh . http://<instance-name>.us-west-2.elb.amazonaws.com
$ source ~/.bashrc && dcos help
```
Now clone the repo in the directory where you've installed the CLI. For example, I've installed into `~/dcos/` hence I would do the following.

```
$ pwd
/home/vagrant/dcos
$ git clone https://github.com/jrx/dcos-training.git
Cloning into 'dcos-training'...
remote: Counting objects: 4, done.
remote: Compressing objects: 100% (4/4), done.
remote: Total 4 (delta 0), reused 4 (delta 0), pack-reused 0
Unpacking objects: 100% (4/4), done.
Checking connectivity... done.
$ ll
total 36
drwxrwxr-x 6 vagrant vagrant 4096 Mar 24 10:43 ./
drwxr-xr-x 6 vagrant vagrant 4096 Mar 24 10:25 ../
drwxrwxr-x 2 vagrant vagrant 4096 Mar 24 10:25 bin/
drwxrwxr-x 3 vagrant vagrant 4096 Mar 24 10:43 dcos-training/
-rw-rw-r-- 1 vagrant vagrant 5250 Mar 24 10:24 install.sh
drwxrwxr-x 3 vagrant vagrant 4096 Mar 24 10:25 lib/
drwxrwxr-x 2 vagrant vagrant 4096 Mar 24 10:25 local/
-rw-rw-r-- 1 vagrant vagrant   60 Mar 24 10:25 pip-selfcheck.json
```

Going forward, we will call the directory you've installed the DCOS CLI in simply  `$DCOS_CLI_HOME`.

Now check if you can access the DCOS cluster dashboard and you're all set:

![DCOS Dashboard](img/dcos-dashboard.png)

The hands on sessions are:

1. [Containers &amp; Docker](./docker)
1. [Mesos &amp; Marathon](./mesos-marathon)
1. [Mesos &amp; Marathon Advanced](./advanced)
1. [DCOS Infinity](./tweeter)

## Resources

- [DCOS doc](https://docs.mesosphere.com)
- [Docker doc](https://docs.docker.com/)
- [Mesos doc](http://mesos.apache.org/documentation/latest/) | [Marathon doc](https://mesosphere.github.io/marathon/docs/)
