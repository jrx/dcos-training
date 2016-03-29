# DCOS Infinity Demo

Tweeter is a sample app that demonstrates how easy it is to run a Twitter-like app on DCOS.
[Sources on github](https://github.com/mesosphere/oinker/tree/tweeter)

You'll need a DCOS cluster with one public node and at least five private nodes.

Capabilities:

- stores tweets in Cassandra
- streams tweets to Kafka as they come in
- real time tweet analytics on via Spark and Zeppelin

## Placeholders
- $DCOS_CLI_HOME: DCOS command line tool directory
- $MASTER_IP_ADDRESS: Mesos Master IP address
- $PUBLIC_IP_ADDRESS: Public slave IP of the EC2 Host

## Configure the Vagrant Instance

```
$ pwd
/home/vagrant/dcos
$ git clone https://github.com/mesosphere/oinker.git --branch tweeter
Cloning into 'oinker'...
remote: Counting objects: 625, done.
remote: Total 625 (delta 0), reused 0 (delta 0), pack-reused 625
Receiving objects: 100% (625/625), 3.24 MiB | 832.00 KiB/s, done.
Resolving deltas: 100% (270/270), done.
Checking connectivity... done.
```

You'll need Ruby and a couple of libraries on your local machine to hack on this app, and to send tweets to it. So you need to setup the Ruby environment, install the Ruby package manageer and Tweeter's dependencies within you the directory of the repo:

```
$ cd /home/vagrant/dcos/oinker
$ sudo apt-get install rbenv nodejs openjdk-8-jdk
$ rbenv init
$ sudo gem install bundler
$ bundle install
```

## Install and Configure Prerequisites on the Cluster

Add the Multiverse as a package source:
```
$ dcos package repo add multiverse https://github.com/mesosphere/multiverse/archive/version-2.x.zip
```

Install packages:

```
$ dcos package install cassandra --yes
$ dcos package install marathon-lb --yes
$ dcos package install kafka --yes
```

## Start Kafka brokers

Wait until the Kafka service shows up in the DCOS UI, then add and start the Kafka brokers:

```
$ dcos kafka broker add 0,1,2
$ dcos kafka broker start 0,1,2
```

## Run the Tweeter App

Launch three instances of Tweeter on Marathon using the config file in this repo:

```
$ cd /home/vagrant/dcos/oinker
$ dcos marathon app add marathon.json
$ dcos marathon app list
```

The app talks to Cassandra via `cassandra-dcos-node.cassandra.dcos.mesos`, and Kafka via `broker-0.kafka.mesos:1025`. If your cluster uses different names for Cassandra or Kafka, edit marathon.json first.

Traffic is routed to the app via marathon-lb. Navigate to `http://$PUBLIC_IP_ADDRESS:10000` to see the Tweeter UI and post a Tweet.

## Post a lot of Tweets

Post a lot of Shakespeare tweets from a file:

```
$ cd /home/vagrant/dcos/oinker
$ ./bin/rake shakespeare:tweet ./shakespeare-data.json http://$PUBLIC_IP_ADDRESS:10000
```

This will post more than 100k tweets one by one, so you'll see them coming in steadily when you refresh the page.

## Streaming Analytics

Next, we'll do real-time analytics on the stream of tweets coming in from Kafka.

Install the Zeppelin package:

```
$ dcos package install --yes zeppelin
```

Add the role slave_public to the Zeppelin marathon app so that marathon launches it on the public slave.

Navigate to Zeppelin at `http://$PUBLIC_IP_ADDRESS:<marathon port>` and load the Spark Notebook from `spark-notebook.json`. Zeppelin is preconfigured to execute Spark jobs on the DCOS cluster, so there is no further configuration or setup required.

Run the Load Dependencies step to load the required libraries into Zeppelin. Next, run the Spark Streaming step, which reads the tweet stream from Zookeeper, and puts them into a temporary table that can be queried using SparkSQL. Next, run the Top Tweeters SQL query, which counts the number of tweets per user, using the table created in the previous step. The table updates continuously as new tweets come in, so re-running the query will produce a different result every time.

## Scale Tweeter

```
$ dcos marathon app update tweeter instances=4
```

## CleanUp Database

```
$ dcos marathon app add cassandra-clear.json
$ dcos marathon app remove cassandra-clear
```
