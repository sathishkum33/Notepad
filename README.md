#!/bin/bash

echo "=== Airflow Health Check ==="

# 1. Check current executor
echo -n "Executor: "
podman exec airflow-webserver python -c "from airflow.configuration import conf; print(conf.get('core', 'executor'))"

# 2. Check Celery worker status
echo -e "\nCelery Worker Status:"
podman exec airflow-webserver airflow celery status || echo "Unable to fetch Celery worker status"

# 3. Check Airflow DB connection
echo -e "\nChecking DB Connection:"
podman exec airflow-webserver airflow db check || echo "DB connection check failed"

# 4. Check Redis connection from worker
echo -e "\nChecking Redis from Worker:"
podman exec airflow-worker python -c "import redis; r = redis.Redis(host='redis', port=6379); print('Redis ping:', r.ping())" 2>/dev/null || echo "Redis ping failed"

# 5. Optional: Check webserver status
echo -e "\nWebserver Port Check (8080):"
podman exec airflow-webserver curl -s --fail http://localhost:8080/health || echo "Webserver not responding"

echo -e "\n=== Health Check Complete ==="