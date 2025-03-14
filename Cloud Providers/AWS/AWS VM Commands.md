# Create a security group for security tools
aws ec2 create-security-group \
    --group-name security-tools-sg \
    --description "Security group for ISO 27001 security tools" \
    --vpc-id vpc-xxxxxxxx

# Allow inbound access for management (SSH) and monitoring tools
aws ec2 authorize-security-group-ingress \
    --group-id sg-xxxxxxxxxxxxxxxxx \
    --protocol tcp \
    --port 22 \
    --cidr 10.0.0.0/16

# Create a monitoring server instance
aws ec2 run-instances \
    --image-id ami-xxxxxxxxxxxxxxxxx \
    --count 1 \
    --instance-type t3.large \
    --key-name security-key-pair \
    --security-group-ids sg-xxxxxxxxxxxxxxxxx \
    --subnet-id subnet-xxxxxxxxxxxxxxxxx \
    --block-device-mappings "[{\"DeviceName\":\"/dev/sda1\",\"Ebs\":{\"VolumeSize\":100,\"DeleteOnTermination\":false}}]" \
    --tag-specifications "ResourceType=instance,Tags=[{Key=Name,Value=security-monitoring-server},{Key=Purpose,Value=ISO27001}]"

# Wait for instance to be running
aws ec2 wait instance-running --instance-ids i-xxxxxxxxxxxxxxxxx

# Allocate and associate Elastic IP (optional)
aws ec2 allocate-address --domain vpc
aws ec2 associate-address --instance-id i-xxxxxxxxxxxxxxxxx --allocation-id eipalloc-xxxxxxxxxxxxxxxxx