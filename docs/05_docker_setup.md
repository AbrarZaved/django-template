# Docker Setup

This project is fully dockerized for easy development, testing, and production deployment. The setup includes a `Dockerfile`, `docker-compose.yml`, and an `entrypoint.sh` script.

## Using Docker Compose (Recommended)

Docker Compose is the easiest way to spin up the application, as it can orchestrate the Django app along with a PostgreSQL database and Redis if needed.

### 1. Build and Run
Make sure you have copied `.env.example` to `.env` first.
```bash
docker-compose up -d --build
```
This command will:
- Build the Docker image for the Django app.
- Start the application container.
- Map port `8000` to your host machine.

### 2. View Logs
To follow the logs of the running container:
```bash
docker-compose logs -f
```

### 3. Stop the Containers
```bash
docker-compose down
```

## Manual Docker Commands

If you prefer using vanilla Docker without compose:

### 1. Build the Image
```bash
docker build -t django-template .
```

### 2. Run the Container
```bash
docker run -p 8000:8000 --env-file .env -v $(pwd):/app django-template
```

## The Entrypoint Script (`entrypoint.sh`)
When the container starts, it executes `entrypoint.sh`. This script automates common Django startup tasks:
- **Migrations**: Runs `python manage.py migrate` automatically.
- **Static Files**: Runs `python manage.py collectstatic --no-input`.
- **Gunicorn Server**: Starts the WSGI server for production-ready performance.

## Executing Commands Inside the Container

To run management commands (like creating a superuser) while the container is running via compose:
```bash
docker-compose exec web python manage.py createsuperuser
```
*(Note: Replace `web` with the exact service name defined in `docker-compose.yml`)*
