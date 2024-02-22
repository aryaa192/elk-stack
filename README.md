# elk-stack
All you need to know about elk-stack from a logging-system perspective.


# What is ELK or EFK stack?
It's an abbreviation of three different open-source projects now used combined and supported by elastic.
E - Elasticsearch (Analytics and Searchengine with NoSQL type data storage platform)
L | F - Logstash or Fluentbit (Data parser|Extraction|Filteration tools.)
K - Kibana (A visualisation dashboard integrating with elasticsearch makes the data readable and represents it more meaningful way.)

### Some important key factors to know about elasticsearch:
- It's Database solutions desinged with analytics and search capabilities.
- It uses NoSQL database system.
- It uses Inverted Index data-structure from behind the scene.
- It's search capabilities are implemented based on one of [oracle project Lucene SearchEngine](https://lucene.apache.org/) project.
- We can run this as --> standalone software as service on Linux-server | Containerized | Cloud based solution.
- Minimum System requirement is: [min 4 CPU\vCPU, 8GB RAM, 50GB ROM, 1gbps network bandwidth for production systems] (Note: elasticsearch is requires jdk/jre env and it's uses a lot of heap memory [know more about heap memory usage and garbage collections](https://opster.com/guides/elasticsearch/capacity-planning/elasticsearch-heap-size-usage/) )

### Some important key factors to know about Logstash/Fluentbit/beats:
- It's ETL --> Extract,Tranform,Load pipeline solution.
- There're various types of beats apart from Logstash that can be implement as ETL pipeline solution.(E.g., Filebeat,Fluentbit or Fluentd).
- Based on the use cases and system requirements we can opt out the best fit solutions.
- Fluentbit/Filebeat are lightweight solution as compare with Logstash. (We can also apply them combined if requires.)


### Some important key factors to know about Kibana:
- It's visualisation tools that integrates with elasticsearch making the logging-system even more powerful.
- It provides rich GUI feature that helps to interact with logs/data stored in elasticsearch.
- It has stack-monitoring portal through which one can track the realtime state of elk-cluster.
- It comes with dev-tools that helps you do the CRUD operation on elasticsearch. Apart from that using DSL we can troubleshoot the cluster-health, indices related issue, etc..,.


### Setup ELK Stack on local environment:

Please follow this repo: [elk-docker](https://github.com/aryaa192/elk-docker)

Steps:
- Docker, Docker-Compose are prerequisite to run this locally.
- clone this repo.
- go to the cloned directory.
- run command:
  ```
  docker compose up -d
  ```
- you can do the config change in each config files. (requires good understanding on docker-compose and elk setup)
  #### Troubleshoot guide:
  - [memory related issue](https://discuss.elastic.co/t/understanding-docker-and-memory-settings/123336/3)
  - [security related changes](https://www.elastic.co/guide/en/elasticsearch/reference/current/security-basic-setup.html)
  - Network related issue: Usually, from setup perspective it doesn't happen. In case, Please check if the ports are correctly exposed, check bridge network connections.
 

### Setup Multi-clustered ELK on production environment:

![elk-setup-ha](https://github.com/aryaa192/elk-stack/assets/22274075/67f66dac-32de-4607-af10-c41decf77f1e)

### setting-up elasticsearch cluster at on-premese data-center.
#### System requirements:
- 3 nodes for elasticsearch, 3 nodes for kibana, 1 node for Logstash, 1 node for kafka.
- [64 GB RAM, data storage min 1tb, 8 cpu/vcpus] each for elasticsearch.

> Step-1: download & install [elasticsearch](https://www.elastic.co/downloads/elasticsearch) (do it for all three nodes.)

> Step-2: download & install logstash & kibana to respective nodes/server machine.

> Step-3: configure elasticsearch cluster.

#### elasticsearch confgiuration 
- elasticsearch.yml for configuring Elasticsearch
- jvm.options for configuring Elasticsearch JVM settings
- log4j2.properties for configuring Elasticsearch logging
#### certificates required for to establish secure connections between elasticsearch nodes and between elasticsearch and kibana.
- SSL certificates (for elasticsearch nodes connections)
- Http certificate (for elasticsearch and kibana connection)

> step-1 go to one of elasticsearch server/node

> step-2 go to /usr/share/elasticsearch/bin

> generate below certificates:
  - ca certificates (this is required for to generate cert certificates which will use for as ssl certificate.)
    command: ```./elasticsearch-certutil ca```
  > output: /usr/share/elasticsearch/elastic-stack-ca.p12 
  
  - cert certificates (This certificate is used for establish secure connection between elasticsearch nodes.)
    command: ```./elasticsearch-certutil cert --ca elastic-stack-ca.p12 ```
  > output: /usr/share/elasticsearch/elastic-certificates.p12

  - http certificate (this will be used for to establish connection between elasticsearch and kibana)
    command: ``` ./elasticsearch-certutil http ```

    Note: This will ask few details to generate the certs.
    - CSR (Certificate Sigining Requests) (This is used when you require an exisitng certificate authority that you do not own or manage.) (Select No)
    - Use an existing CA (So, we have our own signed ca generated by us. we can use that.) (Select yes)
    - We need to provide the ca with full path. (input: /usr/share/elasticsearch/elastic-stack-ca.p12)
    - ask passphrase (setting passphrase is a good practice) (optional, If you have set any password while creating ca then we need to provide here.)
    - set certificate validity. (default 5y) (optional)
    - generate certificates per node? (No, means it is going to generate one certificate can be use in each node.) (select no)
    - Enter hostnames: (provide all the hostnames)
    - Enter the IPs: (provide the IPs if the client is trying to connect via the numeric Ips)
    - It will summary. and ask if you wish to change any details. (cross check.) (select no)
    - provide password. (setting password is good practice.)(optional)
    - set filename. (default: [/usr/share/elasticsearch/elasticsearch-ssl-http.zip]) (optional)
> output: /usr/share/elasticsearch/elasticsearch-ssl-http.zip

  Note: This .zip contains (This will be moved to require path to be used by elasticsearch and kibana.)
  - elasticsearch/http.p12
  - kibana/elasticsearch-ca.pem

 
