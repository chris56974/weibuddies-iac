# kafka

Kafka needs to run as a stateful set because a stateful set maintains a sticky identity for each pod (and each broker needs a unique ID). We don't want to reach for a kafka broker at random, we want to reach for a specific kafka broker. A statelessSet also provides persistence via a persistent volume.

## The kafka/config/kraft/readme.md with my own edits

To bring up a kraft cluster...

```bash
# generate a cluster ID for your new kafka cluster
./bin/kafka-storage.sh random-uuid # xtzWWN4bTjitpL3kfd9s5g

# format the storage directories for each node (make sure to use the same clusterID for each node)
./bin/kafka-storage.sh format -t <uuid> -c ./config/kraft/server.properties 

# then start the Kafka server on each node using the properties you like
./bin/kafka-server-start.sh ./config/kraft/server.properties
```

## Overview

In kraft mode, you have two types of nodes - controllers & brokers. A broker receives messages from producers and stores them on disk keyed by unique offset. A controller is responsible for managing the state of the cluster (partitions, replicas) and you typically need 3-5 for availability. You must tell each node whether it's a controller, a broker, or both using the process.roles property (kafka/config/kraft/server.properties file). Nodes that are both controllers and brokers are convenient but if they crash on the broker side, then they also crash on the controller side (and you always need a majority of controllers to run for the quorum). The "quorum" part refers to how controllers all agree with each other on what the cluster state/metadata is. Every node (broker or controller) has to know who all the controllers are. You do that by enumerating the controllers in the `controller.quorum.voters` key in the same kafka/config/kraft/server.properties file. An example config might look like this.

```bash
process.roles=controller # this is a controller
node.id=1                # this is the 1st controller
listeners=CONTROLLER://controller1.example.com:9093 # other controllers can reach me here

# You list a controller like this -> id@host:port
# So in a cluster with three controllers, you list all of them like this
controller.quorum.voters=1@controller1.example.com:9093,2@controller2.example.com:9093,3@controller3.example.com:9093
```