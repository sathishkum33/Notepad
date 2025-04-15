version: "3"

services:
  web:
    build:
      context: .
      dockerfile: Dockerfile   # optional; remove this if you use image directly
    image: my-django-ubi9
    container_name: django-app
    working_dir: /app
    command: >
      bash -c "pip install --upgrade pip &&
               pip install -r requirements.txt &&
               python manage.py runserver 0.0.0.0:8000"
    volumes:
      - /home/airflow/my_django_project:/app:Z
    ports:
      - "8000:8000"
    user: "4416:4417"