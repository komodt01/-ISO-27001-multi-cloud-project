# Update system
sudo apt-get update && sudo apt-get upgrade -y

# Install dependencies
sudo apt-get install -y apt-transport-https ca-certificates curl software-properties-common

# Install Docker (useful for containerized monitoring tools)
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
sudo apt-get update
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
sudo usermod -aG docker $USER

# Install Docker Compose
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

# Create directory for ELK stack
mkdir -p ~/elk-stack
cd ~/elk-stack

# Create docker-compose.yml for ELK stack
cat > docker-compose.yml << 'EOF'
version: '3'
services:
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:7.16.2
    container_name: elasticsearch
    environment:
      - discovery.type=single-node
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    volumes:
      - elasticsearch-data:/usr/share/elasticsearch/data
    ports:
      - 9200:9200
    networks:
      - elk

  logstash:
    image: docker.elastic.co/logstash/logstash:7.16.2
    container_name: logstash
    ports:
      - 5044:5044
      - 5000:5000/tcp
      - 5000:5000/udp
      - 9600:9600
    environment:
      LS_JAVA_OPTS: "-Xmx256m -Xms256m"
    volumes:
      - ./logstash/config/logstash.yml:/usr/share/logstash/config/logstash.yml
      - ./logstash/pipeline:/usr/share/logstash/pipeline
    networks:
      - elk
    depends_on:
      - elasticsearch

  kibana:
    image: docker.elastic.co/kibana/kibana:7.16.2
    container_name: kibana
    ports:
      - 5601:5601
    environment:
      ELASTICSEARCH_URL: http://elasticsearch:9200
      ELASTICSEARCH_HOSTS: http://elasticsearch:9200
    networks:
      - elk
    depends_on:
      - elasticsearch

networks:
  elk:
    driver: bridge

volumes:
  elasticsearch-data:
    driver: local
EOF

# Create Logstash configuration directories
mkdir -p logstash/config logstash/pipeline

# Create Logstash configuration
cat > logstash/config/logstash.yml << 'EOF'
http.host: "0.0.0.0"
xpack.monitoring.elasticsearch.hosts: [ "http://elasticsearch:9200" ]
EOF

# Create a basic pipeline for cloud logs
cat > logstash/pipeline/cloud-logs.conf << 'EOF'
input {
  tcp {
    port => 5000
    codec => json
  }
}

filter {
  if [cloud_provider] == "aws" {
    mutate {
      add_tag => [ "aws" ]
    }
  }
  else if [cloud_provider] == "azure" {
    mutate {
      add_tag => [ "azure" ]
    }
  }
  else if [cloud_provider] == "gcp" {
    mutate {
      add_tag => [ "gcp" ]
    }
  }
}

output {
  elasticsearch {
    hosts => ["elasticsearch:9200"]
    index => "cloud-logs-%{+YYYY.MM.dd}"
  }
}
EOF

# Start the ELK stack
sudo docker-compose up -d