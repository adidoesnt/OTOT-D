# Setup
- Set up 3 instances of zookeeper
    - Important to do this first because broker cannot run without zookeeper
- Set up 3 instances of broker (this is the kafka cluster)

# Using the cluster
- Create topic (normal)
    ```
    bin/kafka-topics.sh --create --topic test-topic --bootstrap-server localhost:9092
    ```
- Create topic (docker)
    ```
    docker exec broker1 kafka-topics --create --topic test-topic --bootstrap-server broker1:19092
    ```
    - any of the brokers can be used to create the topic
    - topics can be spread across partitions (default is 1)
    - replication factor replicates messages to different brokers
- Create producer and write messages (normal)
    ```
    bin/kafka-console-producer.sh --topic test-topic --broker-list localhost:9092
    ```
-  Create producer and write messages (docker)
    ```
    docker exec --interactive --tty broker1 kafka-console-producer --topic test-topic --bootstrap-server broker1:19092
    ```
- Create consumer and write messages (normal)
    ```
    bin/kafka-console-consumer.sh --topic test-topic --bootstrap-server localhost:9092
    ```
- Create consumer and write messages (docker)
    ```
    docker exec --interactive --tty broker1 kafka-console-consumer --topic test-topic --bootstrap-server broker1:19092
    ```
    or
    ```
    docker exec --interactive --tty broker1 kafka-console-consumer --topic test-topic --bootstrap-server broker1:19092 --from-beginning
    ```
- __Have both producer and consumer running__ or __Use "from beginning" option__
- Send message via producer, should see the message in consumer

# Docker
- run docker-compose up -d
- run (create topic):
    ```
    docker exec broker1 kafka-topics --create --topic test-topic --bootstrap-server broker1:19092 --replication-factor 3
    ```
- run (start producer):
    ```
    docker exec --interactive --tty broker1 kafka-console-producer --topic test-topic --bootstrap-server broker1:19092
    ```
- run (in separate terminal, start consumer):
    ```
    docker exec --interactive --tty broker1 kafka-console-consumer --topic test-topic --bootstrap-server broker1:19092 --from-beginning
    ```
- run (check leader for partition):
    ```
    docker exec broker1 kafka-topics --topic test-topic --bootstrap-server broker1:19092 --describe
    ```
- stop container broker1 using docker/docker desktop (simulate leader broker failure)
- run (start new producer):
    ```
    docker exec --interactive --tty broker2 kafka-console-producer --topic test-topic --bootstrap-server broker2:19093
    ```
- run (in separate terminal, start new consumer):
    ```
    docker exec --interactive --tty broker2 kafka-console-consumer --topic test-topic --bootstrap-server broker2:19093 --from-beginning
    ```
    - observe previous messages still preserved
- run (check leader for partition):
    ```
    docker exec broker2 kafka-topics --topic test-topic --bootstrap-server broker2:19093 --describe
    ```
    - observe that broker2 has taken over as lead for partition 0 which was initially led by broker1