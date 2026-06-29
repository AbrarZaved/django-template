# Naming Conventions & Rules

To maintain a clean and professional codebase, all developers must strictly follow these naming conventions. Consistent naming makes the project easier to read, maintain, and scale.

## 1. Files and Folders (Directories)
- **Format**: `snake_case` (all lowercase, separated by underscores).
- **App Names**: Should generally be singular (e.g., `user`, `contact`, `product` — NOT `users` or `products`).
- **File Names**: E.g., `views.py`, `api_view.py`, `email_utils.py`.

## 2. Classes (Models, Serializers, Views)
- **Format**: `PascalCase` (Capitalize every word, no spaces or underscores).
- **Models**: Singular noun. (e.g., `User`, `Product`, `ContactUs`).
- **Serializers**: Append `Serializer` to the model name. (e.g., `UserSerializer`, `ProductListSerializer`).
- **Views**: Append the View type. (e.g., `ProductListCreateAPIView`, `UserProfileAPIView`, `FAQRetrieveUpdateDestroyAPIView`).

## 3. Functions and Methods
- **Format**: `snake_case` (all lowercase, separated by underscores).
- **Rule**: Should sound like an action/verb. 
- **Examples**: `get_queryset()`, `update_last_active()`, `send_verification_email()`.

## 4. Variables
- **Format**: `snake_case`.
- **Rule**: Use descriptive, readable names. Avoid single-letter variables unless used as quick iterators in simple loops.
- **Examples**: `user_profile`, `is_active`, `product_list`.
- **Booleans**: Prefix with `is_`, `has_`, or `can_` (e.g., `is_verified`, `has_permission`).

## 5. Constants and Enums
- **Format**: `UPPER_SNAKE_CASE` (all uppercase, separated by underscores).
- **Rule**: Use for variables that do not change during runtime.
- **Examples**: `MAX_LOGIN_ATTEMPTS`, `DEFAULT_PAGINATION_SIZE`.
- **Enums**: Located in `enums.py` (e.g., `USER_ROLE.ADMIN`, `STATUS.ACTIVE`).

## 6. URLs and Endpoints
- **Format**: `kebab-case` (all lowercase, separated by hyphens).
- **Rule**: Endpoints should be pluralized nouns (for resources) or descriptive actions. Always include a trailing slash `/`.
- **Examples**: 
  - `api/v1/users/` (Good)
  - `api/v1/auth/verify-email/` (Good)
  - `api/v1/UserProfile` (Bad)
  - `api/v1/products_list` (Bad)

## 7. Model Fields
- **Format**: `snake_case`.
- **Rule**: No need to prefix with the table name (e.g., use `title`, not `product_title` inside the `Product` model).
- **Foreign Keys**: End with the related model name in lowercase, but Django automatically appends `_id` in the database. (e.g., `user = models.ForeignKey(...)`).
