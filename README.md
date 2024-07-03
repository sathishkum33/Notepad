# Load the XML file
[xml]$xmlConfig = Get-Content -Path "C:\path\to\your\web.config"

# Iterate over each location tag to find the appSettings with DBCONNECTIONSTRING
foreach ($location in $xmlConfig.configuration.'location') {
    $dbConnectionString = $location.appSettings.add | Where-Object { $_.key -eq "DBCONNECTIONSTRING" }
    if ($dbConnectionString) {
        # Output the value if found
        $dbConnectionString.value
    }
}
