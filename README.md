#!/bin/bash

# Define variables
NEW_JAR_URL="http://artifacr.com/test_job-4.10.20240910.2.jar"
NEW_JAR_NAME="test_job-4.10.20240910.2.jar"
TARGET_JAR_NAME="test_job.jar"

# Download the new jar
echo "Downloading the new jar..."
curl -O $NEW_JAR_URL

# Check if the old jar exists and remove it
if [ -f "$TARGET_JAR_NAME" ]; then
    echo "Old jar exists, removing $TARGET_JAR_NAME..."
    rm -f $TARGET_JAR_NAME
else
    echo "No existing jar found with name $TARGET_JAR_NAME."
fi

# Rename the new jar to remove the version
echo "Renaming $NEW_JAR_NAME to $TARGET_JAR_NAME..."
mv $NEW_JAR_NAME $TARGET_JAR_NAME

echo "Process completed."