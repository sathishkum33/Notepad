# Path to the web.config file
$webConfigPath = "C:\path\to\your\web.config"

# Load the web.config file
[xml]$webConfig = Get-Content $webConfigPath

# Define the key you want to change and the new value
$key = "YourAppSettingKey"
$newValue = "NewValue"

# Find the appSetting with the specified key
$appSetting = $webConfig.configuration.appSettings.add | Where-Object { $_.key -eq $key }

if ($appSetting) {
    # Update the value
    $appSetting.value = $newValue

    # Save the updated web.config file
    $webConfig.Save($webConfigPath)

    Write-Output "The value of '$key' has been updated to '$newValue'."
} else {
    Write-Output "AppSetting with key '$key' not found."
}
