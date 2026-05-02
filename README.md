#!/bin/bash
# start_airflow.sh
# Exit on any error
set -e

echo "Starting all Airflow components..."

# Start components in the background and store their PIDs
airflow scheduler &
PIDS[0]=$!

airflow dag-processor &
PIDS[1]=$!

airflow triggerer &
PIDS[2]=$!

airflow api-server --port 8080 &
PIDS[3]=$!

# Function to clean up and kill all background processes
cleanup() {
    echo "Shutting down all Airflow components..."
    for pid in "${PIDS[@]}"; do
        kill "$pid" 2>/dev/null
    done
}

# Trap EXIT and INT signals to run cleanup
trap cleanup EXIT INT

# Wait for all background processes
wait