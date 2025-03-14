# Create DLP inspection template
gcloud dlp templates create \
    --location=global \
    --template-id=sensitive-data-template \
    --inspect-template-name=projects/PROJECT-ID/locations/global/inspectTemplates/sensitive-data-template \
    --display-name="Sensitive Data Template" \
    --description="Template to identify PII and sensitive data" \
    --info-types=CREDIT_CARD_NUMBER,PHONE_NUMBER,EMAIL_ADDRESS,US_SOCIAL_SECURITY_NUMBER

# Create DLP inspection job
gcloud dlp jobs create \
    --project=PROJECT-ID \
    --location=global \
    --inspect-template-name=projects/PROJECT-ID/locations/global/inspectTemplates/sensitive-data-template \
    --storage-config-cloud-storage-options-file-set-patterns-include-regex="gs://BUCKET-NAME/*" \
    --actions-save-findings-output-config-table-project-id=PROJECT-ID \
    --actions-save-findings-output-config-table-dataset-id=dlp_findings \
    --actions-save-findings-output-config-table-table-id=findings