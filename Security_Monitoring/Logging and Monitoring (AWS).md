# Enable CloudTrail
aws cloudtrail create-trail \
    --name security-trail \
    --s3-bucket-name cloudtrail-logs-bucket \
    --is-multi-region-trail \
    --enable-log-file-validation

aws cloudtrail start-logging --name security-trail

# Set up CloudWatch log group for sensitive data access
aws logs create-log-group --log-group-name data-access-logs

# Create CloudWatch metric filter for sensitive data access
aws logs put-metric-filter \
    --log-group-name data-access-logs \
    --filter-name "SensitiveDataAccess" \
    --filter-pattern "{$.eventName = \"GetItem\" && $.requestParameters.tableName = \"sensitive-table\"}" \
    --metric-transformations \
        metricName=SensitiveDataAccessCount,metricNamespace=SecurityMetrics,metricValue=1

# Create CloudWatch alarm
aws cloudwatch put-metric-alarm \
    --alarm-name SensitiveDataAccessAlert \
    --metric-name SensitiveDataAccessCount \
    --namespace SecurityMetrics \
    --statistic Sum \
    --period 300 \
    --evaluation-periods 1 \
    --threshold 10 \
    --comparison-operator GreaterThanThreshold \
    --alarm-actions arn:aws:sns:REGION:ACCOUNT-ID:security-alerts