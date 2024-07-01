# Define Vault parameters
$vaultAddress = "http://127.0.0.1:8200" # Vault server address
$roleID = "your-role-id" # AppRole Role ID (replace with your actual Role ID)
$secretID = "your-secret-id" # AppRole Secret ID (replace with your actual Secret ID)
$secretPath = "secret/data/my-secret" # Path to the secret

# Define the Vault URL for the AppRole login
$vaultLoginUrl = "$vaultAddress/v1/auth/approle/login"

# Set up the body for the request
$loginBody = @{
    role_id = $roleID
    secret_id = $secretID
} | ConvertTo-Json

# Send a POST request to the Vault API to authenticate and fetch the token
$loginResponse = Invoke-RestMethod -Uri $vaultLoginUrl -Method Post -Body $loginBody -ContentType "application/json"

# Extract the token from the response
$vaultToken = $loginResponse.auth.client_token

# Output the token
Write-Output "Vault Token: $vaultToken"

# Define the Vault URL for the secret
$vaultSecretUrl = "$vaultAddress/v1/$secretPath"

# Set up the headers for the request
$headers = @{
    "X-Vault-Token" = $vaultToken
}

# Send a GET request to the Vault API to fetch the secret
$secretResponse = Invoke-RestMethod -Uri $vaultSecretUrl -Method Get -Headers $headers

# Extract the password from the response
$password = $secretResponse.data.data.password

# Output the password
Write-Output "Password: $password"
