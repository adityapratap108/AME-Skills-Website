# 01_Software_Requirement_Specification.md

## Part 6 - Authentication, Authorization, Security and Privacy

**Project Name:** AME AI Tutor

**Document ID:** SRS-01-Part-6

**Version:** 1.0

**Status:** Draft

---

# 1. Security Overview

The AME AI Tutor system must protect:

- User accounts
- User profile information
- Uploaded PDFs
- PDF processing data
- Study history
- Quiz history
- Quiz scores
- AI-generated content
- API credentials
- Internal system services

The system must ensure that users can access only the data and resources they are authorized to access.

---

# 2. User Authentication

## FR-121 - User Authentication

The system must require users to authenticate before accessing protected application features.

Unauthenticated users should not be able to access:

- User Dashboard
- Uploaded PDFs
- Study Mode
- Quiz Mode
- Study History
- Quiz History
- User Progress
- User Scores

---

# 3. User Registration

## FR-122 - User Registration

The system should allow new users to create an account.

The registration process should collect the required user information.

At minimum, the system should support:

- Name
- Email Address
- Password

The system should validate the submitted information before creating an account.

---

# 4. Email Validation

## FR-123 - Validate Email

The system should validate the email address during registration.

The email address should:

- Follow a valid email format.
- Be unique within the system.

A user should not be able to create multiple accounts using the same email address.

---

# 5. Password Security

## FR-124 - Secure Password Storage

User passwords must never be stored in plain text.

Passwords must be securely hashed before being stored in the database.

The system should use a modern password hashing algorithm supported by the backend framework.

The original password must not be recoverable from the stored password hash.

---

# 6. Login

## FR-125 - User Login

Registered users should be able to log in using:

- Email Address
- Password

The system should validate the credentials.

If the credentials are valid, the system should create an authenticated session.

If the credentials are invalid, the system should return a generic authentication error.

The system should not reveal whether the email address or password was incorrect.

---

# 7. Logout

## FR-126 - User Logout

Authenticated users should be able to log out.

After logout:

- The authentication session should be invalidated.
- Protected API requests should no longer be accessible using the invalidated authentication state.

---

# 8. Authentication Session

The system should maintain an authenticated state for logged-in users.

The authentication mechanism should be handled by the backend.

The React frontend should not directly manage sensitive authentication credentials in insecure storage.

The exact authentication implementation will be defined in the Technical Architecture Document.

---

# 9. Unauthorized Access

## FR-127 - Prevent Unauthorized Access

If an unauthenticated user attempts to access a protected resource, the system should reject the request.

Example:

Unauthenticated User

↓

Request User Dashboard

↓

Backend Rejects Request

↓

Authentication Required

---

# 10. Authorization

## FR-128 - Resource Authorization

The backend must verify that the authenticated user has permission to access a requested resource.

Authorization checks must be performed server-side.

The frontend must not be trusted as the only authorization layer.

---

# 11. User-Owned PDF Access

## FR-129 - Protect Uploaded PDFs

A user should only be able to access PDFs that belong to that user or that the user has explicitly been granted access to.

The system must prevent direct unauthorized access to another user's PDF.

---

# 12. PDF Access Control

The backend should verify ownership or access permission before allowing operations such as:

- View PDF
- Download PDF
- Study PDF
- Start Quiz
- View PDF Progress
- View Study History
- View Quiz History
- Delete PDF

---

# 13. PDF Storage Security

Original PDFs stored in Cloudflare R2 should not be publicly accessible by default.

The system should use secure access mechanisms when users need to access their PDFs.

The frontend should not expose permanent public storage URLs for private user PDFs.

---

# 14. Secure PDF Access

When a user needs to view or download a private PDF, the backend should verify authorization before providing access.

The system may use temporary secure URLs or a controlled backend access mechanism.

The exact implementation will be defined in the Technical Architecture Document.

---

# 15. User Data Isolation

## FR-130 - Isolate User Data

The system must logically isolate data belonging to different users.

User A must not be able to access:

- User B's PDFs
- User B's Study History
- User B's Quiz History
- User B's Scores
- User B's Progress
- User B's AI-generated quiz data

---

# 16. AI Retrieval Security

## FR-131 - Secure RAG Retrieval

The RAG retrieval system must respect user authorization.

When retrieving content for an AI request, the system must ensure that the retrieved chunks belong to:

- The current user's authorized PDF
- The current PDF
- The current topic where applicable

The AI must not retrieve another user's private PDF content.

---

# 17. Cross-User Data Leakage Prevention

The system must prevent cross-user data leakage through:

- API requests
- Database queries
- Vector searches
- AI prompts
- RAG retrieval
- PDF storage
- Cached data

Every relevant request should be scoped to the authorized user or resource.

---

# 18. API Key Security

## FR-132 - Protect AI API Keys

Gemini API keys must remain server-side.

API keys must never be:

- Embedded in React code
- Sent to the browser
- Exposed through frontend API responses
- Stored in public repositories
- Hard-coded into source code

The Python AI Service should communicate with Gemini using server-side credentials.

---

# 19. Environment Variables

Sensitive configuration should be stored using secure environment configuration.

Examples include:

- Gemini API Keys
- Database Credentials
- Cloudflare R2 Credentials
- Internal Service Credentials
- Application Secrets

Sensitive values should not be committed to Git repositories.

---

# 20. Multiple Gemini API Keys

If multiple Gemini API keys are configured for the application, they must remain server-side.

The frontend should never know:

- How many keys exist
- Which key is currently being used
- Which key failed
- Which key reached a rate limit

The backend or Python AI Service should manage API key selection.

---

# 21. API Key Rate Limit Handling

If a Gemini API key reaches its applicable rate limit, the AI service may attempt to use another configured key according to the application's server-side key management logic.

The system must not expose API keys to users.

If all configured keys are unavailable, the system should return a user-friendly error.

Example:

> AI service is temporarily unavailable. Please try again later.

---

# 22. API Key Failure Handling

The system should distinguish between temporary and permanent API failures where possible.

Examples:

Temporary:

- Rate limit
- Temporary service unavailability
- Network timeout

Permanent or configuration-related:

- Invalid API key
- Invalid request
- Unsupported model

The system should handle each failure appropriately.

---

# 23. Frontend Security

The React frontend should not contain:

- Gemini API keys
- Database credentials
- Cloudflare R2 secret keys
- Internal Python AI Service credentials
- Laravel application secrets

The frontend should communicate only with the application's public API layer.

---

# 24. Laravel-Python Service Security

The Laravel backend and Python AI Service communicate through internal APIs.

The Python AI Service should not be publicly accessible without appropriate protection.

Internal requests should be authenticated or otherwise securely validated.

The exact mechanism will be defined in the Technical Architecture Document.

---

# 25. Input Validation

## FR-133 - Validate User Input

The backend must validate user input before processing.

Validation should apply to:

- Registration
- Login
- PDF Upload
- Study Questions
- Quiz Answers
- Voice Answer Metadata
- API Parameters

The system should reject invalid input.

---

# 26. PDF Upload Security

Uploaded PDFs should be validated before processing.

The system should validate:

- File Extension
- MIME Type
- File Size
- File Integrity

The system should not rely only on the file extension.

---

# 27. Malicious File Protection

The system should reduce the risk of malicious file uploads.

The upload pipeline should reject files that do not meet the expected PDF validation requirements.

The original file should be treated as untrusted input.

---

# 28. File Processing Isolation

PDF processing should be isolated from the main application where practical.

The Python AI Service should process uploaded files in a controlled environment.

Processing should not allow uploaded PDF content to execute arbitrary system commands.

---

# 29. Database Security

The database should not be directly accessible from the React frontend.

Database credentials must remain server-side.

The application should use:

- Secure database credentials
- Least-privilege database access
- Parameterized queries or ORM-based queries
- Appropriate database access controls

---

# 30. SQL Injection Protection

The system must protect against SQL injection.

The backend should use safe database access methods.

User-provided values must not be directly concatenated into raw SQL queries without proper parameterization.

---

# 31. API Security

All protected API endpoints must require appropriate authentication.

The backend should verify:

- Authentication
- Authorization
- Request validity

before returning protected data.

---

# 32. Rate Limiting

The system should apply rate limiting to sensitive or resource-intensive endpoints.

Rate limiting should be considered for:

- Login
- Registration
- PDF Upload
- AI Requests
- Quiz Generation
- Answer Evaluation

The exact limits will be defined during technical implementation.

---

# 33. AI Request Abuse Prevention

The system should prevent users from unnecessarily generating excessive AI requests.

The backend should control AI requests rather than allowing the frontend to directly call Gemini.

The system should apply appropriate request controls.

---

# 34. API Error Responses

API error responses should not expose sensitive internal information.

The system should not return:

- Database credentials
- API keys
- Stack traces
- Internal file paths
- Internal service secrets

to the frontend.

---

# 35. Logging

The system should maintain application logs required for troubleshooting and security monitoring.

Logs may include:

- Authentication Events
- PDF Processing Events
- AI Service Errors
- API Errors
- System Errors

---

# 36. Sensitive Data in Logs

The system should not log sensitive information unnecessarily.

Logs should not contain:

- Passwords
- API Keys
- Database Credentials
- Authentication Tokens
- Private Secrets

---

# 37. AI Prompt Privacy

The system should only send the content required for the current AI operation to the AI provider.

The system should avoid unnecessarily sending:

- User authentication information
- Passwords
- Internal credentials
- Unrelated user data

The AI prompt should contain only relevant PDF content and required task context.

---

# 38. User Privacy

The system should treat uploaded PDFs and learning activity as private user data.

This includes:

- Uploaded PDFs
- Study History
- Study Time
- Quiz Answers
- Quiz Scores
- Progress
- Voice Answer Transcriptions

The system should not expose this information to other users.

---

# 39. Voice Answer Privacy

Voice answers should be handled as user-generated data.

If voice recordings are stored, access should be restricted to the authorized user and system services that require access.

Voice transcriptions should also be protected as user data.

---

# 40. Data Ownership

The system should associate uploaded PDFs and learning records with the user account that created them.

User-specific data should remain associated with the correct user throughout:

- Upload
- Processing
- Study
- Quiz
- Reporting

---

# 41. Account Data Access

The user should be able to access their own:

- Profile
- PDFs
- Study History
- Quiz History
- Progress
- Scores

The system should enforce authorization for all such requests.

---

# 42. Account Deletion

If account deletion is implemented, the system should define how user-owned data is handled.

User-related data may include:

- User Account
- Uploaded PDFs
- Processed PDF Data
- Images
- Chunks
- Embeddings
- Study Sessions
- Quiz Sessions
- Quiz Attempts
- Scores

The exact deletion and retention policy will be defined according to the client's requirements and applicable privacy requirements.

---

# 43. Data Deletion

If a user deletes an uploaded PDF, the system should handle associated data appropriately.

Associated data may include:

- Original PDF
- Extracted Images
- Extracted Tables
- Text Chunks
- Embeddings
- Topic Data
- Study History
- Quiz History

The exact deletion behavior will be finalized in the technical design.

---

# 44. HTTPS

All production application communication should use HTTPS.

This should include communication between:

- Browser and Laravel API
- Browser and application frontend
- Laravel and publicly exposed services

Internal service communication should also be secured where required by the deployment environment.

---

# 45. Secure Transport

Sensitive data should not be transmitted over unencrypted HTTP connections in production.

The application should use secure transport for:

- Authentication
- PDF Upload
- API Requests
- AI Requests
- User Data

---

# 46. Session Security

Authentication sessions should be protected against common session-related attacks.

The implementation should use appropriate security controls supported by the selected authentication architecture.

---

# 47. Authentication Error Handling

Authentication errors should use generic messages.

Example:

> Invalid email or password.

The system should avoid revealing whether a particular email address is registered.

---

# 48. Authorization Error Handling

If a user attempts to access a resource they are not authorized to access, the system should return an appropriate authorization error.

The system should not reveal private information about the requested resource.

---

# 49. Security Boundaries

The application should maintain the following security boundaries:

React Frontend

↓

Public Application API

↓

Laravel Backend

↓

Internal Python AI Service

↓

AI Provider

The React frontend must not directly communicate with:

- Database
- Gemini API
- Internal Python AI Service
- Private R2 credentials

---

# 50. Security Acceptance Criteria

The Security and Authentication System will be considered complete when:

- Users must authenticate to access protected features.
- Passwords are securely hashed.
- Login and logout work correctly.
- Unauthorized users cannot access protected resources.
- Authorization is enforced server-side.
- Users can only access their own PDFs.
- Users can only access their own learning data.
- RAG retrieval cannot expose another user's PDF content.
- Gemini API keys remain server-side.
- Cloudflare R2 credentials remain server-side.
- Database credentials remain server-side.
- Internal Python AI Service is protected.
- User input is validated.
- PDF uploads are validated.
- Malicious or invalid files are rejected.
- SQL injection risks are mitigated.
- Sensitive data is not unnecessarily stored in logs.
- API errors do not expose internal secrets.
- AI prompts do not contain unnecessary sensitive information.
- Voice data is protected.
- Production communication uses HTTPS.
- Rate limiting is applied to sensitive endpoints.
- AI request abuse is controlled.
- User data remains isolated between accounts.

---

# 51. Locked Security Decisions

| Decision             | Requirement                                         |
| -------------------- | --------------------------------------------------- |
| Authentication       | Required for protected application features         |
| Password Storage     | Secure password hashing                             |
| Authorization        | Server-side authorization required                  |
| PDF Access           | User can access only authorized PDFs                |
| RAG Access           | Retrieval must respect PDF/user authorization       |
| Gemini API Keys      | Server-side only                                    |
| Database Credentials | Server-side only                                    |
| R2 Credentials       | Server-side only                                    |
| Frontend Secrets     | No sensitive secrets in React                       |
| Internal AI Service  | Must be protected                                   |
| PDF Validation       | Required before processing                          |
| Input Validation     | Required                                            |
| SQL Injection        | Must be prevented                                   |
| HTTPS                | Required in production                              |
| Rate Limiting        | Required for sensitive/resource-intensive endpoints |
| Logging              | Security and troubleshooting logs required          |
| Sensitive Logs       | Passwords, API keys and secrets must not be logged  |
| User Data            | Must remain isolated between users                  |

---

End of SRS Part 6
