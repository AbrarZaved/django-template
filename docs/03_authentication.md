# Authentication & User Management

This project uses **JWT (JSON Web Tokens)** for authentication via the `djangorestframework_simplejwt` package. The user management is handled entirely by the `user` app.

## Custom User Model
The default Django user model is replaced with a custom `User` model located in `user/models.py`.
- **Primary Key**: UUID (`id`).
- **Extra Fields**: `role`, `status`, `image`, `address1`, `phone1`, `last_active_at`.
- **User Roles**: Managed via `USER_ROLE` enum (SUPER_ADMIN, ADMIN, USER).

## Authentication Flow

### 1. Registration
- **Endpoint**: `POST /api/v1/auth/register/`
- **Action**: Creates a new user account.

### 2. Email Verification
- **Endpoint**: `POST /api/v1/auth/verify-email/`
- **Action**: Verifies the newly registered email address. This step ensures users have valid emails before allowing full access.

### 3. Login
- **Endpoint**: `POST /api/v1/auth/login/email/`
- **Payload**: Requires `email` and `password`.
- **Response**: Returns JWT `access` and `refresh` tokens.
- **Usage**: Include the access token in the headers of subsequent requests:
  ```http
  Authorization: Bearer <your_access_token>
  ```

### 4. Token Refresh
- **Endpoint**: `POST /api/v1/auth/refresh/`
- **Payload**: Requires the `refresh` token.
- **Response**: Returns a new `access` token. Note that access tokens expire quickly (typically 5-15 mins).

## Password Management

### Forgot Password
- **Request OTP**: `POST /api/v1/auth/password/forgot/`
- **Verify OTP**: `POST /api/v1/auth/password/verify/`
- **Reset Password**: `POST /api/v1/auth/password/reset/` (requires the verified token/OTP).

### Change Password
- **Endpoint**: `POST /api/v1/auth/password/change/old/`
- **Usage**: For authenticated users who want to update their password. Requires providing the current password.

## Profiles
- **Get Profile**: `GET /api/v1/auth/profile/`
- **Update Profile**: `PATCH /api/v1/auth/profile/update/`
