# Create a security group for your VPC
aws ec2 create-security-group \
    --group-name iso27001-security-sg \
    --description "Security group for ISO 27001 implementation" \
    --vpc-id vpc-xxxxxxxx

# Add inbound rule for SSH access
aws ec2 authorize-security-group-ingress \
    --group-id sg-xxxxxxxxxxxxxxxxx \
    --protocol tcp \
    --port 22 \
    --cidr 10.0.0.0/16

# Add inbound rule for HTTPS access
aws ec2 authorize-security-group-ingress \
    --group-id sg-xxxxxxxxxxxxxxxxx \
    --protocol tcp \
    --port 443 \
    --cidr 10.0.0.0/16

# Add outbound rule (allows all traffic by default)
aws ec2 authorize-security-group-egress \
    --group-id sg-xxxxxxxxxxxxxxxxx \
    --protocol all \
    --port all \
    --cidr 0.0.0.0/0