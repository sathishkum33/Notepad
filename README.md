FROM apache/airflow:2.9.0  # Use your Airflow version

# Switch to root to install Linux packages
USER root

# Install ping command
RUN apt-get update && apt-get install -y --no-install-recommends \
    iputils-ping \
 && apt-get clean \
 && rm -rf /var/lib/apt/lists/*

# Switch back to airflow user (important for Airflow runtime)
USER airflow