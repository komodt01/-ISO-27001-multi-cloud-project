# Install Grafana
sudo apt-get install -y apt-transport-https software-properties-common
sudo add-apt-repository "deb https://packages.grafana.com/oss/deb stable main"
wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
sudo apt-get update
sudo apt-get install grafana

# Start Grafana
sudo systemctl enable grafana-server
sudo systemctl start grafana-server

# Configure datasources (typically done through UI, but can be scripted)
curl -X POST -H "Content-Type: application/json" -d '{
  "name":"elasticsearch",
  "type":"elasticsearch",
  "url":"http://elasticsearch:9200",
  "access":"proxy",
  "isDefault":true,
  "database":"[logstash-]YYYY.MM.DD",
  "basicAuth":false
}' http://admin:admin@localhost:3000/api/datasources