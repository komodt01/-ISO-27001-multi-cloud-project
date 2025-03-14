# Enable SQL Database dynamic data masking
az sql db update \
    --resource-group rg-name \
    --server server-name \
    --name db-name \
    --dynamic-data-masking-policy '{"rules":[{"column":"SSN","masking":"Number"},{"column":"CreditCard","masking":"Credit Card"}]}'

# Create Information Protection label via PowerShell
# (Azure CLI doesn't fully support this, requires PowerShell)
# Example PowerShell command would be:
# New-AIPLabel -Title "Confidential" -EncryptionEnabled $true