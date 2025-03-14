## SIEM & SOAR Integration

| Component | AWS | Azure | Google Cloud | Cross-Cloud Options |
|-----------|-----|-------|-------------|---------------------|
| SIEM | AWS Security Hub + CloudWatch | Microsoft Sentinel | Google Security Operations | Splunk, Elastic Security, QRadar |
| SOAR | AWS Security Hub | Microsoft Sentinel | Google Security Operations | Splunk SOAR, Palo Alto XSOAR, Swimlane |
| Log Collection | CloudWatch Logs, CloudTrail | Log Analytics | Cloud Logging | Logstash, Fluentd, Vector |
| Alerting | CloudWatch Alarms, EventBridge | Azure Monitor Alerts | Cloud Monitoring Alerting | Prometheus + Alertmanager |
| Compliance Reporting | Security Hub, Audit Manager | Microsoft Defender for Cloud | Security Command Center | Compliance tools in cross-cloud SIEM |
| Threat Intelligence | GuardDuty | Microsoft Defender for Cloud | Security Command Center | MISP, ThreatConnect |
| User Entity Behavior Analytics | Detective | Microsoft Sentinel | Security Command Center | Exabeam, Securonix |

### SIEM Implementation Considerations
1. **Data Volume**: Plan for log storage capacity and retention periods
2. **Query Performance**: Consider log parsing and indexing strategies 
3. **Cross-Cloud Correlation**: Normalize log formats to enable cross-cloud threat detection
4. **Cost Management**: Implement log filtering to control storage and processing costs
5. **ISO 27001 Controls**: Map SIEM data collection to specific control requirements

### SOAR Automation Use Cases
1. **Account Compromise Response**: Automated account locking and investigation
2. **Data Leak Prevention**: Automated remediation of cloud storage misconfigurations
3. **Vulnerability Management**: Automated ticket creation and remediation tracking
4. **Compliance Monitoring**: Automated reporting on ISO 27001 control effectiveness
5. **Threat Hunting**: Automated investigation of suspicious activities

### Cross-Cloud SIEM Architecture
```bash
# Example Fluentd configuration for multi-cloud log collection
<source>
  @type cloudwatch_logs
  region us-east-1
  tag aws.cloudtrail
  log_group_name security-trail
</source>

<source>
  @type azuremonitorlogs
  tag azure.logs
  tenant_id "${AZURE_TENANT_ID}"
  client_id "${AZURE_CLIENT_ID}"
  client_secret "${AZURE_CLIENT_SECRET}"
</source>

<source>
  @type google_cloud_logging
  tag gcp.logs
  project_id "${GCP_PROJECT_ID}"
  key_file "/path/to/service-account.json"
</source>

<filter **>
  @type record_transformer
  <record>
    environment ${tag}
    normalized_timestamp ${time.strftime('%Y-%m-%dT%H:%M:%S.%NZ')}
  </record>
</filter>

<match **>
  @type elasticsearch
  host elasticsearch.local
  port 9200
  logstash_format true
  index_name unified-cloud-logs
</match>