# Load the XML file
[xml]$webConfig = Get-Content "path\to\your\web.config"

# Extract the connection string
$connectionString = $webConfig.configuration.connectionStrings.add | Where-Object { $_.name -eq "YourConnectionStringName" } | Select-Object -ExpandProperty connectionString

# Verify the connection string (example for SQL Server)
try {
    $connection = New-Object System.Data.SqlClient.SqlConnection
    $connection.ConnectionString = $connectionString
    $connection.Open()
    Write-Output "Connection Successful"
    $connection.Close()
} catch {
    Write-Output "Connection Failed: $_"
}
