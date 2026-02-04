# Installation Instructions

#### Linux Setup (Ubuntu / RHEL) <a href="#elasticsearchsetup-and-usageguide-linuxsetup-ubuntu-rhel" id="elasticsearchsetup-and-usageguide-linuxsetup-ubuntu-rhel"></a>

**Step 1: Download Elasticsearch**

{% code overflow="wrap" %}
```
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-9.x.x-linux-x86_64.tar.gz
```
{% endcode %}

**Step 2: Extract the Archive**

```
tar -xzf elasticsearch-9.x.x-linux-x86_64.tar.gz
cd elasticsearch-9.x.x
```

**Step 3: Configure Elasticsearch**

```
Edit config/elasticsearch.yml
```

> cluster.name: elasticsearch-cluster\
> node.name: node-1\
> network.host: 0.0.0.0\
> http.port: 9200\
> discovery.type: single-node

**Step 4: Start Elasticsearch**

```
./bin/elasticsearch
```

**Step 5: Verify Installation**

```
curl http://localhost:9200
```

#### Windows Setup <a href="#elasticsearchsetup-and-usageguide-windowssetup" id="elasticsearchsetup-and-usageguide-windowssetup"></a>

**Step 1: Download Elasticsearch**

* Download the **Windows ZIP** from Elastic official site

**Step 2: Extract ZIP File**

* Extract to a directory (example: `C:\elasticsearch`)

**Step 3: Configure Elasticsearch**

Edit `config\elasticsearch.yml`

> cluster.name: elasticsearch-cluster\
> node.name: node-1\
> network.host: localhost\
> http.port: 9200\
> discovery.type: single-node

**Step 4: Start Elasticsearch**

```
bin\elasticsearch.bat
```

**Step 5: Verify Installation**

Open browser or Postman: _`(Use the password that was automatically generated in the terminal)`_

```
http://localhost:9200
```

#### Docker Setup <a href="#elasticsearchsetup-and-usageguide-dockersetup" id="elasticsearchsetup-and-usageguide-dockersetup"></a>

**Step 1: Create docker-compose.yml**

> version: '3.8'
>
> services:\
> &#x20; elasticsearch:\
> &#x20;   image: docker.elastic.co/elasticsearch/elasticsearch:9.2.4\
> &#x20;   container\_name: amrit-elasticsearch\
> &#x20;   environment:\
> &#x20;     \- discovery.type=single-node\
> &#x20;     \- xpack.security.enabled=true\
> &#x20;     \- xpack.security.http.ssl.enabled=false\
> &#x20;     \- ELASTIC\_PASSWORD=Password\
> &#x20;     \- "ES\_JAVA\_OPTS=-Xms1g -Xmx1g"\
> &#x20;   ports:\
> &#x20;     \- "9200:9200"\
> &#x20;     \- "9300:9300"\
> &#x20;   volumes:\
> &#x20;     \- es-data:/usr/share/elasticsearch/data\
> &#x20;   networks:\
> &#x20;     \- elastic
>
> &#x20; kibana:\
> &#x20;   image: docker.elastic.co/kibana/kibana:9.2.4\
> &#x20;   container\_name: amrit-kibana\
> &#x20;   environment:\
> &#x20;     \- ELASTICSEARCH\_HOSTS=http://elasticsearch:9200\
> &#x20;     \- ELASTICSEARCH\_USERNAME=elastic\
> &#x20;     \- ELASTICSEARCH\_PASSWORD=Password\
> &#x20;   ports:\
> &#x20;     \- "5601:5601"\
> &#x20;   depends\_on:\
> &#x20;     \- elasticsearch\
> &#x20;   networks:\
> &#x20;     \- elastic
>
> volumes:\
> &#x20; es-data:\
> &#x20;   driver: local
>
> networks:\
> &#x20; elastic:\
> &#x20;   driver: bridge

**Step 2: Start Elasticsearch Container**

```
docker compose up -d
```

**Step 3: Verify Installation**

```
curl -u elastic:<Password> http://localhost:9200
```
