# Enable Macie
aws macie2 enable-macie

# Create custom data identifier
aws macie2 create-custom-data-identifier \
    --name "CustomerSSN" \
    --regex "[0-9]{3}-[0-9]{2}-[0-9]{4}" \
    --description "Identifies US Social Security Numbers"

# Start classification job
aws macie2 create-classification-job \
    --name "InitialDataDiscovery" \
    --s3-job-definition '{"bucketDefinitions":[{"accountId":"YOUR-ACCOUNT-ID","buckets":["sensitive-data-bucket"]}]}' \
    --job-type "ONE_TIME" \
    --sampling-percentage 100