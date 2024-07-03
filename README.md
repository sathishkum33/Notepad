# Load the XML file
[xml]$xmlConfig = Get-Content -Path "C:\path\to\your\web.config"

# Retrieve the DBCONNECTIONSTRING value
$dbConnectionString = $xmlConfig.configuration.'location'.appSettings.add | Where-Object { $_.key -eq "DBCONNECTIONSTRING" }

# Output the value
$dbConnectionString.value
