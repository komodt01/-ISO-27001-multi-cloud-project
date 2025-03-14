# Create a deidentification template
gcloud dlp templates create \
    --location=global \
    --deidentify-template-name=projects/PROJECT-ID/locations/global/deidentifyTemplates/mask-pii-template \
    --template-id=mask-pii-template \
    --display-name="Mask PII Template" \
    --description="Template to mask PII data" \
    --deidentify-config-info-type-transformations-transformations-info-types=US_SOCIAL_SECURITY_NUMBER \
    --deidentify-config-info-type-transformations-transformations-primitive-transformation-character-mask-masking-character="#"