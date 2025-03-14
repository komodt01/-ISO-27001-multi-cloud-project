# Create log sink
gcloud logging sinks create security-sink \
    storage.googleapis.com/security-logs-bucket \
    --log-filter='resource.type="gce_instance" AND severity>=WARNING'

# Create log metric
gcloud logging metrics create sensitive-data-access \
    --description="Metric for sensitive data access" \
    --filter='resource.type="table" AND protoPayload.methodName="google.cloud.bigquery.v2.TableDataService.insertAll" AND protoPayload.resourceName="projects/PROJECT-ID/datasets/sensitive-data/tables/customers"'

# Create alerting policy
gcloud alpha monitoring policies create \
    --policy-from-file=policy.json

# Example content for policy.json:
# {
#   "displayName": "Sensitive Data Access Alert",
#   "conditions": [
#     {
#       "displayName": "Metric Threshold Condition",
#       "conditionThreshold": {
#         "filter": "metric.type=\"logging.googleapis.com/user/sensitive-data-access\" resource.type=\"global\"",
#         "comparison": "COMPARISON_GT",
#         "thresholdValue": 5,
#         "duration": "60s",
#         "trigger": {
#           "count": 1
#         }
#       }
#     }
#   ],
#   "alertStrategy": {
#     "autoClose": "604800s"
#   },
#   "combiner": "OR",
#   "notificationChannels": [
#     "projects/PROJECT-ID/notificationChannels/CHANNEL-ID"
#   ]
# }