# DCOS Training

You need a computer with Vagrant (https://www.vagrantup.com) and VirtualBox (https://www.virtualbox.org) installed. Please download the ubuntu box ahead of the training with the command `vagrant box add ubuntu/wily64`.

# Container management with Docker and Mesos/Marathon

## Contents

Initiate the vagrant environment `vagrant init ubuntu/wily64` start the box `vagrant up` and connect to it with `vagrant ssh`.

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
remote: Counting objects: 10, done.
remote: Compressing objects: 100% (10/10), done.
remote: Total 10 (delta 3), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (10/10), done.
Checking connectivity... done.
$ ls -la
total 32
drwxrwxr-x 6 vagrant vagrant 4096 Dec  6 19:06 .
drwxr-xr-x 6 vagrant vagrant 4096 Dec  6 19:01 ..
drwxrwxr-x 2 vagrant vagrant 4096 Dec  6 19:02 bin
-rw-rw-r-- 1 vagrant vagrant 3654 Dec  6 19:02 install.sh
drwxrwxr-x 3 vagrant vagrant 4096 Dec  6 19:02 lib
drwxrwxr-x 2 vagrant vagrant 4096 Dec  6 19:02 local
drwxrwxr-x 3 vagrant vagrant 4096 Dec  6 19:06 dcos-training
-rw-rw-r-- 1 vagrant vagrant   60 Dec  6 19:02 pip-selfcheck.json
```

Going forward, we will call the directory you've installed the DCOS CLI in simply  `$DCOS_CLI_HOME`.

Now check if you can access the DCOS cluster dashboard and you're all set:

![DCOS Dashboard](img/dcos-dashboard.png)

The hands on sessions are:

1. [Containers &amp; Docker](./docker)
1. [Mesos &amp; Marathon](./mesos-marathon)

## Resources

- [DCOS doc](https://docs.mesosphere.com)
- [Docker doc](https://docs.docker.com/)
- [Mesos doc](http://mesos.apache.org/documentation/latest/) | [Marathon doc](https://mesosphere.github.io/marathon/docs/)