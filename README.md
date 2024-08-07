#!/bin/bash

# Function to get the Java version of a process
get_java_version() {
    local pid=$1
    local java_cmd=$(ps -p $pid -o args= | awk '{print $1}')
    $java_cmd -version 2>&1 | head -n 1
}

# Get the list of Java processes (assuming microservices are Java-based)
java_processes=$(ps aux | grep '[j]ava' | awk '{print $2}')

if [ -z "$java_processes" ]; then
    echo "No Java processes found."
    exit 1
fi

echo "Listing all microservice processes and their Java versions:"

for pid in $java_processes; do
    process_info=$(ps -p $pid -o pid,comm,cmd=)
    java_version=$(get_java_version $pid)
    echo "$process_info"
    echo "Java version: $java_version"
    echo "------------------------------------"
done