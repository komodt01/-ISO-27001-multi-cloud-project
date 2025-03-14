# Create a VPC network for security tools
gcloud compute networks create security-network --subnet-mode=auto

# Create a firewall rule for SSH access
gcloud compute firewall-rules create allow-ssh \
    --network security-network \
    --allow tcp:22 \
    --source-ranges 35.235.240.0/20

# Create a subnet for security tools
gcloud compute networks subnets create security-subnet \
    --network=security-network \
    --region=us-central1 \
    --range=10.0.0.0/24

# Create a monitoring server VM
gcloud compute instances create security-monitoring-server \
    --zone=us-central1-a \
    --machine-type=e2-standard-2 \
    --subnet=security-subnet \
    --network-tier=PREMIUM \
    --maintenance-policy=MIGRATE \
    --image-family=ubuntu-2004-lts \
    --image-project=ubuntu-os-cloud \
    --boot-disk-size=100GB \
    --boot-disk-type=pd-balanced \
    --boot-disk-device-name=security-monitoring-server \
    --tags=security,monitoring \
    --metadata=purpose=iso27001

# Create a persistent disk for log storage
gcloud compute disks create security-logs-disk \
    --size=1TB \
    --zone=us-central1-a \
    --type=pd-standard

# Attach the disk to the monitoring server
gcloud compute instances attach-disk security-monitoring-server \
    --disk=security-logs-disk \
    --zone=us-central1-a

# SSH to the instance and format/mount the disk
gcloud compute ssh security-monitoring-server --zone=us-central1-a \
    --command="sudo mkfs.ext4 -m 0 -E lazy_itable_init=0,lazy_journal_init=0,discard /dev/sdc && \
               sudo mkdir -p /mnt/disks/logs && \
               sudo mount -o discard,defaults /dev/sdc /mnt/disks/logs && \
               sudo chmod a+w /mnt/disks/logs && \
               echo UUID=\$(sudo blkid -s UUID -o value /dev/sdc) /mnt/disks/logs ext4 discard,defaults,nofail 0 2 | sudo tee -a /etc/fstab"