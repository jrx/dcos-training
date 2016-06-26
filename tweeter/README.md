# DCOS Infinity Demo

Tweeter is a sample app that demonstrates how easy it is to run a Twitter-like app on DC/OS.
[Sources on github](https://github.com/mesosphere/tweeter/)

You'll need a DCOS cluster with one public node and at least five private nodes.

Capabilities:

- stores tweets in Cassandra
- streams tweets to Kafka as they come in
- real time tweet analytics on via Spark and Zeppelin

## Placeholders
- $MASTER_IP_ADDRESS: Master IP address
- $PUBLIC_SLAVE_ELB: Public Slave ELB DNS Name. Be sure to remove the leading `http://` and the trailing `/` For example: `janrepnak-publicsl-8ho17zhaumau-386931240.eu-central-1.elb.amazonaws.com`

## Configure the Vagrant Instance

```
$ pwd
/home/ubuntu/dcos
$ git clone https://github.com/mesosphere/tweeter.git
Cloning into 'tweeter'...
remote: Counting objects: 646, done.
remote: Total 646 (delta 0), reused 0 (delta 0), pack-reused 646
Receiving objects: 100% (646/646), 5.28 MiB | 1.11 MiB/s, done.
Resolving deltas: 100% (284/284), done.
Checking connectivity... done.
```

## Install and Configure Prerequisites on the Cluster

Install packages:

```
$ dcos package install cassandra
$ dcos package install marathon-lb
$ dcos package install kafka
```

Wait until the Kafka and Cassandra services are healthly. You can check their status with:

```
$ dcos kafka connection
...
$ dcos cassandra connection
...
```

## Edit the Tweeter Service Config

Edit the `HAPROXY_0_VHOST` label in `tweeter.json` to match your public slave ELB hostname `$PUBLIC_SLAVE_ELB`. Be sure to remove the leading `http://` and the trailing `/`.

```json
{
  "labels": {
    "HAPROXY_0_VHOST": "$PUBLIC_SLAVE_ELB"
  }
}
```

## Run the Tweeter Service

Launch three instances of Tweeter on Marathon using the config file in this repo:

```
$ dcos marathon app add tweeter.json
```

The service talks to Cassandra via `node-0.cassandra.mesos:9042`, and Kafka via `broker-0.kafka.mesos:9557` in this example.

Traffic is routed to the service via marathon-lb. Navigate to `http://$PUBLIC_SLAVE_ELB/` to see the Tweeter UI and post a Tweet.

## Post a lot of Tweets

Post a lot of Shakespeare tweets from a file:

```
dcos marathon app add post-tweets.json
```

This will post more than 100k tweets one by one, so you'll see them coming in steadily when you refresh the page. Take a look at the Networking page on the UI to see the load balancing in action.

## Streaming Analytics

Next, we'll do real-time analytics on the stream of tweets coming in from Kafka.

Navigate to Zeppelin at `https://$MASTER_IP_ADDRESS/service/zeppelin/`, click `Import note` and import `tweeter-analytics.json`. Zeppelin is preconfigured to execute Spark jobs on the DCOS cluster, so there is no further configuration or setup required.

Run the *Load Dependencies* step to load the required libraries into Zeppelin. Next, run the *Spark Streaming* step, which reads the tweet stream from Zookeeper, and puts them into a temporary table that can be queried using SparkSQL. Next, run the *Top Tweeters* SQL query, which counts the number of tweets per user, using the table created in the previous step. The table updates continuously as new tweets come in, so re-running the query will produce a different result every time.

## Scale Tweeter

```
$ dcos marathon app update tweeter instances=4
```

## Show Logs

Use the DC/OS CLI to show the logs of the Tweeter app

```
$ dcos task
$ dcos task tweeter
$ dcos task log tweeter
$ dcos task log tweeter --follow
$ dcos task log tweeter stderr
$ dcos task ls tweeter
```

## Uninstall services

You can uninstall the DC/OS services via command line:

```
$ dcos package uninstall cassandra
$ dcos package uninstall kafka
$ dcos package uninstall zeppelin
```

After this run the service cleaner script via command on your master server:

```
docker run mesosphere/janitor /janitor.py -r kafka-role -p kafka-principal -z kafka
docker run mesosphere/janitor /janitor.py -r cassandra-role -p cassandra-principal -z cassandra
```
