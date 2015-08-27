# Kafka / Zookeeper / Cassandra CentOS standlone instances

Easily create a local Kafka instance w/ Zookeeper quorum  + Cassandra instance via Vagrant + Ansible. Or just use the Ansible playbooks.

* Kafka Version : 0.8.2.1
* Cassandra Version : 2.2.0
* Zookeeper version : 3.4.6

## Usage

Depending on your hardware and network connection, the script execution might take between 10-20 minutes.

### 1. Prerequisites

Install Vagrant, VirtualBox and Ansible on your machine

For Mac, this can be done with Homebrew:
```
brew install caskroom/cask/brew-cask
brew cask install virtualbox
brew install vagrant
brew install ansible
```

Make sure you are running Ansible v1.7.2 or higher with `ansible --version`.

For other systems, checkout the installation pages of [Vagrant](https://docs.vagrantup.com/v2/installation/), [VirtualBox](https://www.virtualbox.org/wiki/Downloads) and [Ansible](http://docs.ansible.com/intro_installation.html).

### 2. Clone this repo

```
git clone git@github.com:tquiviger/ansible-kafka-cassandra-standalone.git
cd ansible-kafka-cassandra-standalone
```


### 3. Start the clusters with Vagrant

```
vagrant up --provider=virtualbox
```

### 4. Test your Kafka instance

SSH into your Kafka node
Run:

```
vagrant ssh kafka-node-1
```


Create a topic in kafka-node-1 and play with it

```
export LOG_DIR=/tmp # otherwise we get a warning
cd /etc/kafka_2.11-0.8.2.1

bin/kafka-topics.sh --create --zookeeper zk-node-1:2181 --replication-factor 1 --partitions 1 --topic my-replicated-topic

# Look at the topic description to make sure it is indeed replicated
bin/kafka-topics.sh --describe --zookeeper zk-node-1:2181 --topic my-replicated-topic

# Send a few messages
bin/kafka-console-producer.sh --broker-list localhost:9092 --topic my-replicated-topic
...
my test message 1
my test message 2
^C
```

### 4. Test your Cassandra instance

SSH into your Cassandra node
Run:

```
vagrant ssh cassandra-node-1
```

In the 1st node :

```
/etc/dsc-cassandra-2.2.0/bin/cqlsh 192.168.5.108

CREATE KEYSPACE dev
WITH REPLICATION = { 'class' : 'SimpleStrategy', 'replication_factor' : 1 };
USE dev;

CREATE TABLE test (
id int,
PRIMARY KEY (id));
```
