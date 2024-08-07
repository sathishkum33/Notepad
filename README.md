#!/bin/bash

# Directories to compare
config_dir="config"
backup_dir="config_bkup"

# Loop through each file in the config directory
for config_file in "$config_dir"/*.yml; do
    # Extract the filename without the directory
    filename=$(basename "$config_file")

    # Construct the corresponding backup file path
    backup_file="$backup_dir/$filename"

    # Check if the backup file exists
    if [ -e "$backup_file" ]; then
        echo "Comparing $filename..."
        # Perform the diff
        diff_output=$(diff "$config_file" "$backup_file")

        if [ -n "$diff_output" ]; then
            echo "Differences found in $filename:"
            echo "$diff_output"
        else
            echo "No differences in $filename."
        fi
        echo "------------------------------------"
    else
        echo "Backup file $backup_file not found."
    fi
done