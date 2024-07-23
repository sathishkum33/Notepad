# Define the path to the web.config file
$webConfigPath = "path\to\your\web.config"

# Generate a timestamp for the backup file
$timestamp = Get-Date -Format "yyyyMMdd_HHmmss"
$backupPath = "$webConfigPath" -replace 'web.config', "web.config_$timestamp"

# Backup the original web.config file
Copy-Item -Path $webConfigPath -Destination $backupPath

Write-Output "Backup of web.config created at $backupPath"

# Read the content of the web.config file
$configContent = Get-Content -Raw -Path $webConfigPath

# Perform the string replacement
$updatedConfigContent = $configContent -replace "Driver={SQL Server};", ""

# Save the updated content back to the web.config file
Set-Content -Path $webConfigPath -Value $updatedConfigContent

Write-Output "The connection string has been updated successfully."

# Verify the updated connection string
[xml]$webConfig = Get-Content -Raw -Path $webConfigPath

# Extract the connection string
$connectionStringElement = $webConfig.configuration.connectionStrings.add | Where-Object { $_.name -eq "MyDbConnectionString" }
$connectionString = $connectionStringElement.connectionString
$providerName = $connectionStringElement.providerName

# Verify the connection string
try {
    if ($providerName -eq "System.Data.SqlClient") {
        $connection = New-Object System.Data.SqlClient.SqlConnection
    } elseif ($providerName -eq "MySql.Data.MySqlClient") {
        $connection = New-Object MySql.Data.MySqlClient.MySqlConnection
    } elseif ($providerName -eq "Npgsql") {
        $connection = New-Object Npgsql.NpgsqlConnection
    } elseif ($providerName -eq "Oracle.ManagedDataAccess.Client") {
        $connection = New-Object Oracle.ManagedDataAccess.Client.OracleConnection
    } elseif ($providerName -eq "System.Data.SQLite") {
        $connection = New-Object System.Data.SQLite.SQLiteConnection
    } else {
        throw "Unsupported provider: $providerName"
    }

    $connection.ConnectionString = $connectionString
    $connection.Open()
    Write-Output "Connection Successful"
    $connection.Close()
} catch {
    Write-Output "Connection Failed: $_"
}
