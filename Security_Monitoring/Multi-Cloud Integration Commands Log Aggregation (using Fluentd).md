# Install Fluentd
sudo curl -L https://toolbelt.treasuredata.com/sh/install-debian-buster-td-agent4.sh | sh

# Configure Fluentd for AWS CloudWatch
sudo cat > /etc/td-agent/td-agent.conf << EOF
<source>
  @type cloudwatch_logs
  region us-east-1
  tag aws.cloudtrail
  log_group_name security-trail
  use_log_stream_name_prefix true
  log_stream_name_prefix prod
</source>

<source>
  @type azuremonitorlogs
  tag azure.logs
  tenant_id "YOUR-TENANT-ID"
  subscription_id "YOUR-SUBSCRIPTION-ID"
  client_id "YOUR-CLIENT-ID"
  client_secret "YOUR-CLIENT-SECRET"
</source>

<source>
  @type google_cloud_logging
  tag gcp.logs
  project_id "YOUR-PROJECT-ID"
  key_file "/path/to/service-account.json"
  resource_type "gce_instance"
</source>

<match **>
  @type elasticsearch
  host elasticsearch.example.com
  port 9200
  logstash_format true
</match>
EOF

# Start Fluentd
sudo systemctl restart td-agent