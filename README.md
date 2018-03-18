# Install Apache Kafka using a Ubuntu Server

After you spin up your Ubuntu server in [AWS](https://aws.amazon.com), you will need to open your terminal and follow the simple steps below.

*Take note, these steps were done on a Mac.*

### Open your Terminal 

Write the following command to access your ubuntu server, but ensure you modify the ".pem" file information, as well as your ububtu server number:

`ssh -i ~/.ssh/TaniaG.pem ubuntu@54.172.154.223`

### Create a Kafka Account 

Create a user called kafka using the useradd command:

`sudo useradd kafka -m`

Set the password for the user you created by using passwd command:

`sudo passwd kafka`

Add it to the sudo group so that it has the privileges required to install Kafka's dependencies by using:
`sudo adduser kafka sudo`

The Kafka user is now ready. Log into it using su:

`su - kafka`

### Install Java and Zookeeper

Before installing Java and Zookeeper, update the list of available packages so you are installing the latest versions available in the repository:

`sudo apt-get update`

As Apache Kafka needs a Java runtime environment, use apt-get to install the default-jre package:

`sudo apt-get install default-jre`

Since the ZooKeeper package is available in Ubuntu's default repositories, install it using apt-get as well:

`sudo apt-get install zookeeperd`

After the installation completes, ZooKeeper will be started as a daemon automatically. By default, it will listen on port 2181.
To make sure that it is working, connect to it via Telnet:

`telnet localhost 2181`

At the Telnet prompt, type in "ruok" and press ENTER. If everything's working, ZooKeeper will say "imok" and end the Telnet session.

### Download and Extract Kafka Binaries

Now that Java and ZooKeeper are installed, we can now download and extract Kafka.
To start, create a directory called Downloads to store all your downloads.

`mkdir -p ~/Downloads`

Use wget to download the Kafka binaries.

`wget "http://mirror.bit.edu.cn/apache/kafka/1.0.0/kafka_2.11-1.0.0.tgz" -O ~/Downloads/kafka.tgz`

Create a directory called kafka and change to this directory. This will be the base directory of the Kafka installation.

`mkdir -p ~/kafka && cd ~/kafka`

Extract the archive you downloaded using the tar command.

`tar -xvzf ~/Downloads/kafka.tgz --strip 1`

#### Configure the Kafka Server

The next step is to configure the Kakfa server by opening the server.properties using text editor "nano"

`nano ~/kafka/config/server.properties`

By default, Kafka doesn't allow you to delete topics. To be able to delete topics, add the following line at the end of the file:

**delete.topic.enable=true**

Save the file by using control "S" and exit the text editor by using control "X". Make sure the file saved! Or else the following steps won't work. 

### Start the Kafka Server

Run the kafka-server-start.sh script using nohup to start the Kafka server as a background process that is independent of your shell session.

`bin/kafka-server-start.sh config/server.properties`

---
I got an error saying "cannot allocate memory" (errno=12), if you get the same, you can use this command to solve the issue:

`export KAFKA_HEAP_OPTS="-Xmx256M -Xms128M`

Then run the above command again, it should work!

---

### Create a Topic

We can create a topic named "test" with a single partition and only one replica. We can open a new terminal or use the one that is already open. 
If you're opening another terminal, you will need to reconnect to your ubuntu and kafka:

`ssh -i ~/.ssh/TaniaG.pem ubuntu@54.172.154.223`

`su - kafka`

`cd ~/kafka`

Then create your new topic with this command (replace "test" in the command with whatever you want to name your test):

`bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test`

We can now see that topic if we run the list topic command:

`bin/kafka-topics.sh --list --zookeeper localhost:2181`

### Send Messages in the Producer

Kafka comes with a command line client that will take input from a file or from standard input and send it out as messages to the Kafka cluster. 
By default, each line will be sent as a separate message.

Run the producer and then type a few messages into the console to send to the server.

`bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test`

You will see ">" appear in the next line. Write a few words and hit enter, and repeat a few times. 


### Start a Consumer

Kafka also has a command line consumer that will dump out messages to standard output. 
Start by opening another terminal where you will need to reconnect to your ubuntu and kafka:

`ssh -i ~/.ssh/TaniaG.pem ubuntu@54.172.154.223`

`su - kafka`

`cd ~/kafka`

Then enter the following command in order to see the messages the producer sent:

`bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic test --from-beginning`

By using this command you will see everything that was written in the Producer since the beginning on the test topic. 

Also, since you have both commands running in a different terminals, you can play around and type messages into the producer terminal and see them appear in the consumer terminal.


#### Good luck! 
