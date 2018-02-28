# ELK and Filebeat
ELK log analyse system deployment guide with and without filebeat
## Requirements
Java 1.8
docker
curl

## ELK and Filebeat Installation
- Pull ELK docker images 
```bash
docker pull docker.elastic.co/elasticsearch/elasticsearch:6.0.0
docker pull docker.elastic.co/kibana/kibana:6.0.0
docker pull docker.elastic.co/logstash/logstash:6.0.0
```
- Install filebeat
```bash
curl -L -O https://artifacts.elastic.co/downloads/beats/filebeat/filebeat-6.0.1-amd64.deb
sudo dpkg -i filebeat-6.0.1-amd64.deb
```

## ELK without Filebeat
- run containers
```bash
sudo docker run --rm -it --name elasticsearch -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" -e "xpack.security.enabled=false" docker.elastic.co/elasticsearch/elasticsearch:6.0.0
sudo docker run --rm -it --name kibana --add-host elasticsearch:172.17.0.1 -p 5601:5601 docker.elastic.co/kibana/kibana:6.0.0
sudo docker run --rm -it --name logstash -p 5044:5044 --add-host elasticsearch:172.17.0.1 -v /home/lucas/logstash:/data docker.elastic.co/logstash/logstash:6.0.0 -f /data/log.conf
```

## ELK with Filebeat
- Edit the config file `/etc/filebeat/filebeat.yml` as [filebeat.yml](/yuchen/logstash_system/logstash/filebeat.yml), config log path and ELK ip address. 
- Launch ELK in docker:
```bash
sudo docker run --rm -it --name elasticsearch -p 9200:9200 -p 9300:9300 -e "discovery.type=single-node" -e "xpack.security.enabled=false" docker.elastic.co/elasticsearch/elasticsearch:6.0.0
sudo docker run --rm -it --name kibana --add-host elasticsearch:172.17.0.1 -p 5601:5601 docker.elastic.co/kibana/kibana:6.0.0
sudo docker run --rm -it --name logstash -p 5044:5044 --add-host elasticsearch:172.17.0.1 -v /home/lucas/logstash:/data docker.elastic.co/logstash/logstash:6.0.0 -f /data/log_filebeat.conf
```
- Load the index template in Elasticsearch
```bash
sudo filebeat export template > filebeat.template.json
curl -XPUT -H 'Content-Type: application/json' http://localhost:9200/_template/filebeat-6.0.1 -d@filebeat.template.json
```
- Launch filebeat:
```bash
sudo filebeat -e
```
## UIs
- access web UI at:

    elasticsearch: http://127.0.0.1:9200/
    
    kibana: http://127.0.0.1:5601/
