# Auth Service methods

This folder contains the proto definitions for the services in the project.

## Auth Service

This service handles user authentication and authorization. It provides endpoints for user registration, login, password reset, and token management.

### Features

- User registration
- User login
- Password reset
- OTP verification

### New Account Creation Flow

1. User initiates account creation with email or username (InitiateAccountCreationRequest)
2. Server generates and sends OTP to user's email
3. User submits OTP for verification (VerifyOTPRequest)
4. Server verifies OTP, returns temporary token (VerifyOTPResponse)
5. User submits account details with temporary token (CreateAccountRequest)
   - Includes email, username, and other required fields
6. Server creates account, returns session token and user info (CreateAccountResponse)

Note: CreateAccountResponse includes a session token for accessing other services.

### Login Flow

1. User submits login credentials (LoginRequest)
   - Email or username, and password
2. Server validates credentials
3. If valid:
   - Server generates session token
   - Server returns session token and user info (LoginResponse)
4. If invalid:
   - Server returns error message (LoginResponse)

Optional: Two-Factor Authentication (2FA)
If 2FA is enabled for the account:
5. Server sends OTP to user's registered 2FA method (e.g., email, SMS)
6. User submits OTP (VerifyLoginOTPRequest)
7. Server verifies OTP

8. If valid:
   - Server generates session token
   - Server returns session token and user info (VerifyLoginOTPResponse)
9. If invalid:
   - Server returns error message (VerifyLoginOTPResponse)

Note: LoginResponse or VerifyLoginOTPResponse (if 2FA enabled) includes a session token for accessing other services.

### Forgot Password Flow

1. User initiates password reset (InitiatePasswordResetRequest)
   - Provides email or username
2. Server verifies account exists
3. If account exists:
   - Server generates and sends OTP to user's registered email
   - Server returns success message (InitiatePasswordResetResponse)
4. If account doesn't exist:
   - Server returns error message (InitiatePasswordResetResponse)

5. User submits OTP for verification (VerifyPasswordResetOTPRequest)
6. Server verifies OTP
7. If OTP is valid:
   - Server generates temporary reset token
   - Server returns temporary reset token (VerifyPasswordResetOTPResponse)
8. If OTP is invalid:
   - Server returns error message (VerifyPasswordResetOTPResponse)

9. User submits new password with temporary reset token (ResetPasswordRequest)
10. Server verifies temporary reset token
11. If token is valid:
    - Server updates user's password
    - Server returns success message (ResetPasswordResponse)
12. If token is invalid:
    - Server returns error message (ResetPasswordResponse)

Note: All temporary tokens (OTP, reset token) should have a short expiration time for security.
