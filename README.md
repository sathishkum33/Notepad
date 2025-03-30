#!/bin/bash

# Set Airflow environment variables
export AIRFLOW_HOME=/opt/app/airflow
export PATH=$PATH:/opt/app/.local/bin
export AIRFLOW__CORE__LOAD_EXAMPLES=False
export AIRFLOW__WEBSERVER__EXPOSE_CONFIG=True

# Initialize Airflow metadata database (if not already initialized)
if [ ! -f "$AIRFLOW_HOME/airflow.db" ]; then
    airflow db init
fi

# Start the Airflow scheduler in the background
airflow scheduler &

# Start the Airflow webserver
exec airflow webserver