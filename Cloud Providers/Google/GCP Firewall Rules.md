# Create firewall rule for SSH access
gcloud compute firewall-rules create allow-ssh-internal \
    --network security-network \
    --action allow \
    --direction ingress \
    --rules tcp:22 \
    --source-ranges 10.0.0.0/16 \
    --priority 1000 \
    --target-tags security-monitoring

# Create firewall rule for HTTPS access
gcloud compute firewall-rules create allow-https-internal \
    --network security-network \
    --action allow \
    --direction ingress \
    --rules tcp:443 \
    --source-ranges 10.0.0.0/16 \
    --priority 1010 \
    --target-tags security-monitoring

# Create a tag for security monitoring VMs
gcloud compute instances add-tags security-monitoring-server \
    --tags security-monitoring \
    --zone us-central1-a