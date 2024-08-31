# Auth Service Methods

This folder contains the proto definitions for the services in the project.

## Auth Service

This service handles user authentication and authorization. It provides endpoints for user registration, login, password reset, and token management.

### Features

- User registration
- User login
- Password reset
- OTP verification

Each flow such as login, account creation, etc. goes through multiple gRPC calls.
Each call generates a new token with specific permissions.

### New Account Creation Flow

AuthService provides the following methods to create a new account:

1. InitiateAccountCreation
2. VerifyOtp
3. CreateAccount

Creating an account has the following steps:

1. Client calls the `InitiateAccountCreation` method with the email. 
   Response: OTP sent to email, token with no permissions.
   
   Token structure:
   ```json
   {
      "permissions": [],
      "requested_permissions": ["CreateAccount"]
   }
   ```

2. Client calls the `VerifyOtp` method with the OTP.
   Response: New token with `CreateAccount` permission.
   
   Token structure:
   ```json
   {
      "permissions": ["CreateAccount"],
      "requested_permissions": []
   }
   ```

3. Client calls the `CreateAccount` method with account details.
   Response: Session token for accessing other services.
   
   Token structure:
   ```json
   {
      "permissions": ["*"],
      "requested_permissions": []
   }
   ```

### Login Flow

AuthService provides the following methods for login:

1. Login
2. VerifyOtp (for 2FA)

Login steps:

With 2FA enabled:
1. Client calls `Login` with email and password.
2. Server validates credentials.
3. Server sends OTP to user's email and responds with a token.

   Token structure:
   ```json
   {
      "permissions": [],
      "requested_permissions": []
   }
   ```

4. Client calls `VerifyOtp` with the OTP.
   Response: Session token.

   Token structure:
   ```json
   {
      "permissions": ["*"],
      "requested_permissions": []
   }
   ```

Without 2FA:
1. Client calls `Login` with email and password.
2. Server validates credentials.
3. Server responds with a session token.

### Password Reset Flow

Methods for password reset:

1. InitiatePasswordReset
2. VerifyOtp
3. ResetPassword

Steps:

1. Client calls `InitiatePasswordReset` with username.
   
   Input:
   - `username`: Account username

   Response:
   - `success`: Boolean
   - `message`: String
   - `token`: String for ResetPassword method

   Token structure:
   ```json
   {
      "permissions": [],
      "requested_permissions": ["ResetPassword"]
   }
   ```

2. Client calls `VerifyOtp` with token and OTP.
   Response: New token with `ResetPassword` permission if OTP is valid.

   Token structure:
   ```json
   {
      "permissions": ["ResetPassword"],
      "requested_permissions": []
   }
   ```

3. Client calls `ResetPassword` with token and new password.
