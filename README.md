# Path to the web.config file
$webConfigPath = "/mnt/data/file-CMjB5jeqekthYf1PGCe0yLcA"

# Load the web.config file
[xml]$webConfig = Get-Content $webConfigPath

# Define the key you want to change
$key = "DBCONNECTIONSTRING"

# Find the appSetting with the specified key
$appSetting = $webConfig.configuration.appSettings.add | Where-Object { $_.key -eq $key }

if ($appSetting) {
    # Output the current value
    Write-Output "Current value of '$key': $($appSetting.value)"

    # Define the new value
    $newValue = "new_connection_string"

    # Update the value
    $appSetting.value = $newValue

    # Save the updated web.config file
    $webConfig.Save($webConfigPath)

    Write-Output "The value of '$key' has been updated to '$newValue'."
} else {
    Write-Output "AppSetting with key '$key' not found."
}
