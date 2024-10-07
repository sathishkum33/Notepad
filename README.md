#!/bin/bash

# Define variables
NEW_JAR_URL="http://artifacr.com/test_job-4.10.20240910.2.jar"

# Extract the full file name from the URL
NEW_JAR_NAME=$(basename $NEW_JAR_URL)

# Extract the base jar name without version (everything before the first hyphen)
TARGET_JAR_NAME=$(echo $NEW_JAR_NAME | sed -E 's/-[0-9.]+\.jar$/.jar/')

# Download the new jar
echo "Downloading the new jar: $NEW_JAR_NAME..."
curl -O $NEW_JAR_URL

# Check if the old jar exists as a symlink
if [ -L "$TARGET_JAR_NAME" ]; then
    echo "Old jar is a symlink, removing the symlink $TARGET_JAR_NAME..."
    unlink $TARGET_JAR_NAME
elif [ -f "$TARGET_JAR_NAME" ]; then
    echo "Old jar exists as a regular file, removing $TARGET_JAR_NAME..."
    rm -f $TARGET_JAR_NAME
else
    echo "No existing jar or symlink found with name $TARGET_JAR_NAME."
fi

# Rename the new jar to remove the version
echo "Renaming $NEW_JAR_NAME to $TARGET_JAR_NAME..."
mv $NEW_JAR_NAME $TARGET_JAR_NAME

echo "Process completed."