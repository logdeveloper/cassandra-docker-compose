# cassandra-docker-compose

### 1. Create user defined bridge network
```bash
docker network create --gateway 172.18.0.1 --subnet 172.18.0.0/16 broker-net
```


### 2. Check docker-compose.yml

```yaml
version: "3.8"
services:
  cassandra-0:
    image: cassandra:latest
    restart: always
    networks:
      broker:
        ipv4_address: 172.18.0.10
    ports:
      - 7000:7000
      - 9042:9042
    volumes:
      - /home/cassandra-node-0/commitlog:/var/lib/cassandra/commitlog
      - /home/cassandra-node-0/hints:/var/lib/cassandra/hints
      - /home/cassandra-node-0/data:/var/lib/cassandra/data
      - /home/cassandra-node-0/saved_caches:/var/lib/cassandra/saved_caches
      - /home/cassandra-node-0/logs:/var/log/cassandra
    environment:
      - CASSANDRA_SEEDS=cassandra-0
      - CASSANDRA_CLUSTER_NAME=cluster
      - CASSANDRA_NUM_TOKENS=8
      - CASSANDRA_DC=dc1
      - CASSANDRA_RACK=rack0
      - CASSANDRA_ENDPOINT_SNITCH=GossipingPropertyFileSnitch
      - MAX_HEAP_SIZE=8G
      - HEAP_NEWSIZE=200M

  cassandra-1:
    image: cassandra:latest
    networks:
      broker:
        ipv4_address: 172.18.0.11
    restart: always
    ports:
      - 1700:7000
      - 19042:9042
    volumes:
      - /home/cassandra-node-1/data:/var/lib/cassandra/data
      - /home/cassandra-node-1/commitlog:/var/lib/cassandra/commitlog
      - /home/cassandra-node-1/hints:/var/lib/cassandra/hints
      - /home/cassandra-node-1/saved_caches:/var/lib/cassandra/saved_caches
      - /home/cassandra-node-1/logs:/var/log/cassandra
    environment:
      - CASSANDRA_SEEDS=cassandra-0
      - CASSANDRA_CLUSTER_NAME=cluster
      - CASSANDRA_NUM_TOKENS=8
      - CASSANDRA_DC=dc1
      - CASSANDRA_RACK=rack0
      - CASSANDRA_ENDPOINT_SNITCH=GossipingPropertyFileSnitch      
      - MAX_HEAP_SIZE=8G
      - HEAP_NEWSIZE=200M

  cassandra-2:
    image: cassandra:latest
    restart: always
    networks:
      broker:
        ipv4_address: 172.18.0.12
    ports:
      - 27000:7000
      - 29042:9042
    volumes:
      - /home/cassandra-node-2/data:/var/lib/cassandra/data
      - /home/cassandra-node-2/commitlog:/var/lib/cassandra/commitlog
      - /home/cassandra-node-2/hints:/var/lib/cassandra/hints
      - /home/cassandra-node-2/saved_caches:/var/lib/cassandra/saved_caches
      - /home/cassandra-node-2/logs:/var/log/cassandra      
    environment:
      - CASSANDRA_SEEDS=cassandra-0
      - CASSANDRA_CLUSTER_NAME=cluster
      - CASSANDRA_NUM_TOKENS=8
      - CASSANDRA_DC=dc1
      - CASSANDRA_RACK=rack0
      - CASSANDRA_ENDPOINT_SNITCH=GossipingPropertyFileSnitch
      - MAX_HEAP_SIZE=8G
      - HEAP_NEWSIZE=200M
      
networks:
  broker:
    external:
      name: broker-net     
```



### 3. Run docker-compose.yml

```
$ docker-compose -f docker-compose.yml up -d
```


```
$ docker-compose ps
            Name                          Command               State                                       Ports
------------------------------------------------------------------------------------------------------------------------------------------------------
docker-compose_cassandra-0_1   docker-entrypoint.sh cassa ...   Up      0.0.0.0:7000->7000/tcp, 7001/tcp, 7199/tcp, 0.0.0.0:9042->9042/tcp, 9160/tcp
docker-compose_cassandra-1_1   docker-entrypoint.sh cassa ...   Up      0.0.0.0:17000->7000/tcp, 7001/tcp, 7199/tcp, 0.0.0.0:19042->9042/tcp, 9160/tcp
docker-compose_cassandra-2_1   docker-entrypoint.sh cassa ...   Up      0.0.0.0:27000->7000/tcp, 7001/tcp, 7199/tcp, 0.0.0.0:29042->9042/tcp, 9160/tcp
```


