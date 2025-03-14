# Cloud Provider Equivalency Cheat Sheet

## Compute Resources

| Service Type | AWS | Azure | Google Cloud |
|-------------|-----|-------|--------------|
| Virtual Machines | EC2 Instances | Virtual Machines | Compute Engine |
| VM Size (General Purpose) | t3.medium | Standard_D2s_v3 | e2-standard-2 |
| VM Size (Memory Optimized) | r5.large | Standard_E2s_v3 | e2-highmem-2 |
| VM Size (Compute Optimized) | c5.large | Standard_F2s_v2 | e2-highcpu-2 |
| Container Service | ECS/EKS | AKS | GKE |
| Serverless | Lambda | Functions | Cloud Functions |

## Storage

| Service Type | AWS | Azure | Google Cloud |
|-------------|-----|-------|--------------|
| Object Storage | S3 | Blob Storage | Cloud Storage |
| Block Storage | EBS | Managed Disks | Persistent Disk |
| File Storage | EFS | Azure Files | Filestore |

## Security Services

| Service Type | AWS | Azure | Google Cloud |
|-------------|-----|-------|--------------|
| Identity Management | IAM | Azure AD | Cloud IAM |
| Data Loss Prevention | Macie | Microsoft Purview | Cloud DLP |
| Key Management | KMS | Key Vault | Cloud KMS |
| Firewall | Security Groups, WAF | NSGs, Azure Firewall | VPC Firewall Rules, Cloud Armor |
| Secrets Management | Secrets Manager | Key Vault | Secret Manager |
| Vulnerability Management | Inspector | Defender for Cloud | Security Command Center |
| Policy Management | AWS Config | Azure Policy | Organization Policy Service |

## Data Masking & Protection

| Capability | AWS | Azure | Google Cloud |
|------------|-----|-------|--------------|
| Data Discovery | Macie | Purview | Cloud DLP |
| Masking/Tokenization | Lambda + Macie | Dynamic Data Masking (SQL) | Cloud DLP De-identification |
| Encryption at Rest | S3/EBS/RDS Encryption | Storage Service Encryption | Google Default Encryption |
| Encryption in Transit | ACM + Load Balancers | App Service HTTPS | Load Balancing HTTPS |
| Column-level Encryption | RDS Data API | Always Encrypted | BigQuery AEAD functions |

## Logging & Monitoring

| Service Type | AWS | Azure | Google Cloud |
|-------------|-----|-------|--------------|
| Activity Logs | CloudTrail | Azure Activity Log | Cloud Audit Logs |
| Metrics | CloudWatch Metrics | Azure Monitor Metrics | Cloud Monitoring |
| Log Storage | CloudWatch Logs | Log Analytics | Cloud Logging |
| Log Analysis | CloudWatch Logs Insights | Log Analytics | Log Analytics |
| Security Monitoring | GuardDuty | Microsoft Defender for Cloud | Security Command Center |
| Dashboard | CloudWatch Dashboards | Azure Dashboards | Cloud Monitoring Dashboards |
| Alerting | CloudWatch Alarms | Azure Monitor Alerts | Cloud Monitoring Alerting |

## Networking

| Service Type | AWS | Azure | Google Cloud |
|-------------|-----|-------|--------------|
| Virtual Network | VPC | Virtual Network | VPC |
| Subnet | Subnet | Subnet | Subnet |
| Load Balancer | ELB, ALB, NLB | Azure Load Balancer | Cloud Load Balancing |
| Private Connection | PrivateLink | Private Link | Private Service Connect |
| CDN | CloudFront | Azure CDN | Cloud CDN |

## ISO 27001 Specific Controls Mapping

| ISO 27001 Control | AWS | Azure | Google Cloud |
|-------------------|-----|-------|--------------|
| A.8.2.1 Information Classification | Macie + S3 Object Tags | Azure Information Protection | Cloud DLP + Object Labels |
| A.9.4.1 Access Control | IAM Policies | Azure RBAC | IAM Role Bindings |
| A.10.1.1 Cryptography | KMS + Service Encryption | Key Vault + Service Encryption | Cloud KMS + Default Encryption |
| A.12.4.1 Logging | CloudTrail + CloudWatch Logs | Activity Log + Log Analytics | Cloud Audit Logs + Cloud Logging |
| A.13.1.1 Network Security | Security Groups, NACLs | NSGs, Azure Firewall | Firewall Rules, Cloud Armor |
| A.18.1.3 Data Protection | S3 Object Lock, Glacier Vault Lock | Immutable Blob Storage | Object Retention Policies |