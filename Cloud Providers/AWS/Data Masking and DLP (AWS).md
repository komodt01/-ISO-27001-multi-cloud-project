# Set up Lambda function for data masking (first create function in AWS console or via CloudFormation)
aws lambda update-function-code \
    --function-name DataMaskingFunction \
    --zip-file fileb://masking-function.zip

# Configure DynamoDB encryption
aws dynamodb update-table \
    --table-name sensitive-table \
    --sse-specification Enabled=true,SSEType=KMS,KMSMasterKeyId=KEY-ID