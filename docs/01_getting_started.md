# Getting Started

Welcome to the Angelos Django REST API Template. Follow these instructions to set up the project locally.

## Prerequisites
- **Python 3.10+**
- **PostgreSQL** (Optional, SQLite is used by default)
- **pip** and **virtualenv**

## Quick Setup (Recommended)
You can use the provided bash script to quickly initialize the project:
```bash
chmod +x initial.sh
./initial.sh
```
This script will create a virtual environment, copy the environment file, install dependencies, and run migrations.

## Manual Setup

### 1. Virtual Environment
Create and activate a virtual environment:
```bash
python3 -m venv .venv
source .venv/bin/activate  # On Windows: .venv\Scripts\activate
```

### 2. Install Dependencies
```bash
pip install -r requirements.txt
```

### 3. Environment Variables
Copy the `.env.example` file to `.env`:
```bash
cp .env.example .env
```
Update the `.env` file with your specific configuration (e.g., `DATABASE_URL`, `SECRET_KEY`, `EMAIL_HOST_PASSWORD`).

### 4. Database Migrations
Run the migrations to set up the database schema:
```bash
python manage.py makemigrations
python manage.py migrate
```

### 5. Create Superuser (Optional)
```bash
python manage.py createsuperuser
```

### 6. Run Development Server
```bash
python manage.py runserver
```
The API will be available at `http://127.0.0.1:8000/`. You can access the Swagger UI documentation at `http://127.0.0.1:8000/swagger/`.

## Running Tests
This project uses `pytest`. To run tests:
```bash
pytest
```
