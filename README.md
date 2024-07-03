# Define new values for multiple keys
$newValues = @{
    "DBCONNECTIONSTRING" = "NewConnectionStringValue"
    "DBCONNECTIONSTRING_EUC" = "NewEUCConnectionStringValue"
    "OLEDBCONNECTIONSTRING1" = "NewOLEDBConnectionStringValue1"
    # Add more keys and values as needed
}

# Load the XML file
[xml]$xmlConfig = Get-Content -Path "C:\path\to\your\web.config"

# Iterate over each location tag to find the appSettings and update values
foreach ($location in $xmlConfig.configuration.'location') {
    foreach ($key in $newValues.Keys) {
        $setting = $location.appSettings.add | Where-Object { $_.key -eq $key }
        if ($setting) {
            # Update the value if found
            $setting.value = $newValues[$key]
        }
    }
}

# Save the updated XML back to the file
$xmlConfig.Save("C:\path\to\your\web.config")
