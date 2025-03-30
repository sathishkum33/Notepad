FROM registry.access.redhat.com/ubi9/python-39

# Set work directory
WORKDIR /opt/app

# Install Airflow and dependencies
RUN pip install --no-cache-dir apache-airflow==2.7.3 \
    && mkdir -p /opt/app/airflow \
    && chmod -R 777 /opt/app/airflow

# Copy startup script
COPY run.sh /opt/app/run.sh
RUN chmod +x /opt/app/run.sh

# Expose Airflow Web UI port
EXPOSE 8080

# Set entrypoint
CMD ["/opt/app/run.sh"]