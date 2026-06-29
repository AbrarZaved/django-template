# Project Structure

The project follows a modular and reusable architecture using Django applications to group related functionalities.

## High-Level Overview

```text
.
├── common/              # Global shared resources (BaseModel, enums)
├── contact/             # CMS-like app (Terms, Policy, FAQ, Contact)
├── docs/                # Project documentation
├── helpers/             # Utility functions, API mixins, and decorators
├── projectile/          # Core Django project configuration
├── user/                # Custom User app with JWT Auth
├── manage.py            
├── requirements.txt    
└── Dockerfile, docker-compose.yml, etc.
```

## Application Details

### 1. `projectile` (Core Configuration)
This is the root configuration folder for the Django project.
- **`settings.py`**: Main Django settings, divided logically.
- **`env.py`**: Handles environment variables via `django-environ`. All variables from `.env` must be mapped here.
- **`urls.py`**: Root URL routing, tying together the sub-apps and setting up Swagger/Admin paths.

### 2. `common`
Contains shared modules used across multiple apps.
- **`models.py`**: Houses the abstract `BaseModel` that every other model in the project should inherit. It includes `status`, `created_at`, and `updated_at`.
- **`enums.py`**: Global enums like `STATUS` (ACTIVE, DRAFT, INACTIVE).
- **`backends.py` / `permissions.py`**: Custom auth backends and DRF permissions.

### 3. `helpers`
A utility app designed to standardize coding patterns across the project.
- **`response.py`**: `response` and `error_response` utilities for consistent JSON payloads.
- **`api_view.py`**: Contains `QueryParamsMixin` for filtering, and `@create_view` decorator for DRF GenericAPIView standardization.
- **`cache.py` / `filters.py`**: Reusable caching and query filtering logic.

### 4. `user`
Manages everything related to users and authentication.
- **`models.py`**: Defines the custom UUID-based `User` model, extending `AbstractUser`.
- **`urls.py` & `views.py`**: Authentication endpoints (login, register, token refresh, password resets) and user profile updates.

### 5. `contact`
A built-in generic CMS app suitable for most modern platforms.
- **Models**: `Terms`, `Policy`, `FAQ`, and `ContactUs`.
- Provides fully functional CRUD endpoints for these models to quickly build static pages on the frontend.
