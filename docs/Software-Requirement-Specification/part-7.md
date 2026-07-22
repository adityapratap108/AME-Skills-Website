# 01_Software_Requirement_Specification.md

## Part 7 - API, System Integration, Error Handling and Notifications

**Project Name:** AME AI Tutor

**Document ID:** SRS-01-Part-7

**Version:** 1.0

**Status:** Draft

---

# 1. System Communication Overview

The application will use the following major system components:

- ReactJS Frontend
- Laravel Backend API
- Python AI Service
- PostgreSQL Database
- pgvector
- Cloudflare R2 Storage
- Gemini API

The primary communication flow should be:

ReactJS Frontend

↓

Laravel Backend API

↓

Python AI Service

↓

Gemini API / AI Processing

↓

Python AI Service

↓

Laravel Backend API

↓

ReactJS Frontend

---

# 2. ReactJS Frontend

The ReactJS frontend will be responsible for:

- User Interface
- User Interaction
- Dashboard
- PDF Upload Interface
- PDF Workspace
- Study Mode Interface
- Quiz Interface
- Progress Display
- Quiz Results
- Study Reports
- Error Messages
- Processing Status

The React frontend must not directly communicate with:

- PostgreSQL
- pgvector
- Gemini API
- Private Cloudflare R2 credentials
- Internal Python AI Service credentials

The React frontend should communicate with the Laravel Backend API.

---

# 3. Laravel Backend API

The Laravel Backend will act as the main application backend.

Laravel should be responsible for:

- Authentication
- Authorization
- User Management
- PDF Upload Management
- PDF Ownership
- PDF Metadata
- Study Session Management
- Quiz Session Management
- Progress Tracking
- Score Storage
- History Management
- Dashboard Data
- API Request Validation
- Communication with Python AI Service

Laravel should also control access to user-owned resources.

---

# 4. Python AI Service

The Python AI Service will be responsible for AI and document-processing tasks.

It should handle:

- PDF Processing
- Text Extraction
- OCR Processing
- Image Processing
- Table Processing
- Topic Detection
- Content Chunking
- Embedding Generation
- Vector Retrieval
- RAG Context Creation
- Gemini API Communication
- Study Content Generation
- Quiz Question Generation
- Text Answer Evaluation
- Voice Answer Evaluation

The Python AI Service should not directly manage the application's user authentication.

---

# 5. PostgreSQL

PostgreSQL will be used for application data.

It should store information such as:

- Users
- PDFs
- PDF Metadata
- Topics
- Sections
- Chunks Metadata
- Study Sessions
- Quiz Sessions
- Questions
- Answers
- Scores
- Progress
- History

The exact database schema will be defined separately in the Database Design document.

---

# 6. pgvector

pgvector will be used to store and search vector embeddings.

It will support:

- Semantic Search
- RAG Retrieval
- Topic Retrieval
- Relevant Content Retrieval

Vector searches must respect application-level authorization and PDF ownership.

---

# 7. Cloudflare R2

Cloudflare R2 will be used for file storage.

It may store:

- Original PDFs
- Extracted Images
- Voice Recordings, if stored

Private files should not be publicly accessible by default.

The backend should control access to private files.

---

# 8. Gemini API

The Gemini API will be used by the Python AI Service for AI-related operations.

Possible operations include:

- PDF Context Generation
- Topic Understanding
- Study Explanation
- User Question Answering
- Quiz Generation
- Text Answer Evaluation
- Voice Answer Evaluation

The React frontend must never directly call Gemini.

---

# 9. Main API Communication Flow

The general application flow should be:

User

↓

ReactJS

↓

Laravel API

↓

Business Logic

↓

Python AI Service, if AI processing is required

↓

Database / Vector Database / Storage / Gemini

↓

Laravel API

↓

ReactJS

↓

User

---

# 10. API Authentication

Protected API endpoints must require authentication.

Laravel should verify the authenticated user before processing protected requests.

The frontend should send the required authentication information using the selected authentication mechanism.

The exact authentication implementation will be defined in the Technical Architecture Document.

---

# 11. API Authorization

Authentication alone is not sufficient.

For every protected resource request, Laravel must verify that the authenticated user is authorized to access the requested resource.

Example:

User A

↓

Request PDF ID belonging to User B

↓

Laravel checks ownership

↓

Access Denied

---

# 12. User API Requirements

The API should support operations required for:

- User Registration
- User Login
- User Logout
- User Profile
- User Dashboard

The exact API endpoints will be defined in the API Specification document.

---

# 13. PDF API Requirements

The API should support:

- Upload PDF
- View PDF
- List User PDFs
- View PDF Details
- View Processing Status
- Delete PDF

All PDF operations must verify user authorization.

---

# 14. PDF Upload Flow

The PDF upload flow should be:

User Selects PDF

↓

ReactJS

↓

Laravel API

↓

Validate File

↓

Validate Size

↓

Calculate SHA-256

↓

Check Duplicate

↓

Store PDF in R2

↓

Create PDF Record

↓

Start Processing

↓

Return Processing Status

---

# 15. PDF Processing Flow

After upload:

PDF Stored

↓

Processing Job Started

↓

Python AI Service

↓

PDF Type Detection

↓

Text Extraction

↓

OCR if Required

↓

Image Processing

↓

Table Processing

↓

Topic Detection

↓

Chunking

↓

Embedding Generation

↓

Vector Storage

↓

PDF Context Generation

↓

Processing Complete

↓

PDF Status = Ready

---

# 16. Asynchronous PDF Processing

PDF processing should not block the user's browser request for the entire processing duration.

The upload request should return after the PDF has been accepted and processing has been initiated.

The frontend should then be able to check the processing status.

Example:

Upload Complete

↓

Processing PDF...

↓

Processing...

↓

Ready

---

# 17. PDF Processing Status API

The frontend should be able to retrieve the processing status of a PDF.

Possible user-facing statuses:

- Uploading
- Processing
- Ready
- Failed

The backend may internally maintain more detailed processing states.

---

# 18. Processing Status Updates

The frontend should be able to receive updated processing status.

The implementation may use:

- Polling
- Server-Sent Events
- WebSockets

The exact implementation will be decided during technical architecture.

For Version 1, polling may be used if real-time updates are not required.

---

# 19. Duplicate PDF Flow

When a user uploads a PDF:

ReactJS

↓

Laravel

↓

Generate SHA-256

↓

Check Existing PDF

↓

Duplicate Found

↓

Reuse Existing Processed Data

↓

Create User-PDF Relationship

If no duplicate exists:

↓

Store PDF

↓

Start AI Processing

---

# 20. Study Mode API Requirements

The API should support:

- Start Study Session
- Get PDF Context
- Get Topics
- Get Current Topic
- Get Topic Content
- Ask Study Question
- Get AI Explanation
- Get Source References
- Save Study Progress
- Complete Topic
- Complete Study Session
- Resume Study Session

---

# 21. Start Study Session

The flow should be:

User Selects Study Mode

↓

ReactJS

↓

Laravel API

↓

Verify PDF Access

↓

Create or Resume Study Session

↓

Return Study Context

↓

ReactJS Displays Study Mode

---

# 22. Study Content Request

When the user requests content for a topic:

ReactJS

↓

Laravel API

↓

Verify User and PDF

↓

Request Python AI Service

↓

Retrieve Relevant Chunks

↓

Build RAG Context

↓

Gemini Generates Explanation

↓

Return Explanation + Sources

↓

Laravel

↓

ReactJS

---

# 23. Study Question API

When the user asks a question:

User Question

↓

ReactJS

↓

Laravel API

↓

Verify User and PDF

↓

Send Question + Topic Context to Python AI Service

↓

Vector Retrieval

↓

RAG Context

↓

Gemini

↓

AI Answer

↓

Source References

↓

Laravel

↓

ReactJS

---

# 24. Quiz API Requirements

The API should support:

- Start Quiz
- Resume Quiz
- Get Current Topic
- Get Questions
- Submit Answer
- Skip Question
- Update Answer
- Submit Quiz
- Evaluate Answers
- Get Topic Score
- Get Final Score
- Get Quiz Report
- Get Quiz History

---

# 25. Quiz Generation Flow

When a topic quiz begins:

User Starts Quiz

↓

Laravel

↓

Verify PDF and Topic Access

↓

Python AI Service

↓

Retrieve Topic Content

↓

Retrieve Relevant Chunks

↓

Build RAG Context

↓

Generate Questions

↓

Validate AI Output

↓

Store Questions

↓

Return Quiz

↓

ReactJS

---

# 26. Quiz Answer Submission

The frontend should submit answers through Laravel.

The backend should store the answer associated with:

- User
- PDF
- Quiz Session
- Topic
- Question

The answer should not be considered final until the quiz is submitted.

---

# 27. Answer Modification

Before final quiz submission:

The user should be able to:

- Change an answer
- Select another option
- Change multiple-choice selections
- Update a text answer
- Re-record or update a voice answer where supported

The latest valid answer should be used for final evaluation.

---

# 28. Skip Question API

If the user skips a question:

The system should mark the question as:

Unattempted

The question should receive:

0 Marks

No negative marking should be applied.

The user may return to the question before final submission.

---

# 29. Voice Answer API Flow

Voice answer flow:

User Records Voice

↓

ReactJS

↓

Laravel API

↓

Validate Recording

↓

Python AI Service

↓

Speech-to-Text

↓

Transcription

↓

Retrieve Relevant PDF Context

↓

AI Evaluation

↓

Score

↓

Store Result

↓

Return Result

↓

ReactJS

---

# 30. Text Answer Evaluation Flow

Text answer flow:

User Submits Text Answer

↓

ReactJS

↓

Laravel

↓

Python AI Service

↓

Retrieve Question Context

↓

Retrieve Relevant PDF Content

↓

RAG Context

↓

Gemini Evaluation

↓

Score + Explanation

↓

Laravel

↓

ReactJS

---

# 31. Final Quiz Submission

The final submission flow should be:

User Clicks Submit

↓

ReactJS

↓

Laravel API

↓

Verify Quiz Session

↓

Verify User Authorization

↓

Collect Final Answers

↓

Mark Skipped Questions as Unattempted

↓

Evaluate Required Answers

↓

Calculate Scores

↓

Calculate Topic Scores

↓

Calculate Final Score

↓

Determine Pass/Fail

↓

Store Final Attempt

↓

Lock Quiz Attempt

↓

Return Final Report

---

# 32. Quiz Submission Lock

After final submission:

- Answers cannot be modified.
- Quiz attempt cannot be changed.
- Final score becomes permanent for that attempt.
- Final report becomes available.

---

# 33. Dashboard API Requirements

The API should provide data required by the dashboard.

It should support:

- User Information
- Uploaded PDFs
- PDF Status
- Study Progress
- Quiz Progress
- Study Time
- Quiz Scores
- Quiz History
- Study History

---

# 34. PDF Progress API

The API should provide:

- Study Progress
- Quiz Progress
- Completed Topics
- Total Topics
- Current Topic
- Total Study Time
- Latest Quiz Score
- Best Quiz Score

---

# 35. Study History API

The API should provide:

- Study Sessions
- Study Duration
- Topics Studied
- Topics Completed
- Study Dates

The user should only receive their own study history.

---

# 36. Quiz History API

The API should provide:

- Quiz Attempts
- Attempt Dates
- Scores
- Pass/Fail Status
- Topic Scores
- Correct Answers
- Incorrect Answers
- Unattempted Questions

---

# 37. API Error Response Standard

The backend should return consistent error responses.

Errors should contain enough information for the frontend to display an appropriate message.

Error responses should not expose:

- Stack Traces
- API Keys
- Database Credentials
- Internal Secrets
- Internal File Paths

---

# 38. HTTP Status Codes

The API should use appropriate HTTP status codes.

Examples:

200

Successful request.

201

Resource successfully created.

202

Request accepted for asynchronous processing.

400

Invalid request.

401

Authentication required or invalid authentication.

403

Authenticated but not authorized.

404

Requested resource not found.

409

Conflict, such as duplicate resource where applicable.

422

Validation error.

429

Rate limit exceeded.

500

Internal server error.

502

Bad gateway or upstream service failure.

503

Service temporarily unavailable.

---

# 39. Validation Errors

If the user submits invalid data, the API should return validation errors.

Example:

Invalid PDF

↓

Laravel Validation

↓

422 Response

↓

ReactJS Displays Error

The response should identify the relevant validation issue without exposing internal details.

---

# 40. PDF Processing Errors

If PDF processing fails:

Python AI Service

↓

Processing Failure

↓

Laravel Updates PDF Status

↓

PDF Status = Failed

↓

ReactJS Shows Error

Example:

> We could not process this PDF. Please try again later.

---

# 41. AI Service Errors

If the Python AI Service is unavailable:

Laravel should not crash.

The system should return a user-friendly error.

Example:

> AI processing is temporarily unavailable. Please try again later.

---

# 42. Gemini API Errors

The Python AI Service should handle Gemini API errors.

Possible errors include:

- Rate Limit
- Temporary Service Error
- Timeout
- Invalid API Key
- Invalid Request
- Model Error

The AI service should handle these errors appropriately.

---

# 43. Gemini Rate Limit Handling

If one configured Gemini API key reaches its rate limit:

The Python AI Service may attempt another configured API key according to the server-side key management system.

The frontend should not know which API key is being used.

If all configured keys are unavailable:

The system should return:

> AI service is temporarily unavailable. Please try again later.

---

# 44. AI Request Timeout

If an AI request takes longer than the allowed processing time:

The request should timeout safely.

The system should not leave the application waiting indefinitely.

The user should receive an appropriate message.

Example:

> The AI request took too long to complete. Please try again.

---

# 45. Database Errors

If a database operation fails:

The system should:

- Log the error securely.
- Return a generic error to the user.
- Avoid exposing database details.

Example:

> Something went wrong. Please try again later.

---

# 46. Storage Errors

If Cloudflare R2 fails during:

- Upload
- Download
- Image Retrieval
- File Access

the system should handle the error gracefully.

The user should receive an appropriate message.

---

# 47. Network Failure

If the user's internet connection is interrupted:

The frontend should handle the failure gracefully.

For operations where data may be lost, the system should avoid falsely showing the operation as successful.

The system should allow the user to retry where appropriate.

---

# 48. Retry Handling

The system should support retrying operations that fail due to temporary errors.

Retryable operations may include:

- PDF Processing
- AI Requests
- Temporary Storage Operations

Retries should not create duplicate records or duplicate quiz attempts.

---

# 49. Duplicate Request Protection

The backend should prevent accidental duplicate operations where possible.

Examples:

- Double PDF Upload
- Double Quiz Submission
- Repeated Study Session Creation
- Duplicate AI Processing

The system should use appropriate identifiers or idempotency mechanisms where required.

---

# 50. AI Output Validation

AI-generated responses should be validated before being used by the application.

For quiz generation, validation should check:

- Question Exists
- Valid Question Type
- Valid Options
- Correct Answer Exists
- Difficulty Exists
- Source Reference Exists Where Required

Invalid responses should be rejected or regenerated.

---

# 51. AI Response Failure

If AI-generated content cannot be generated successfully:

The system should not save invalid AI output as valid content.

The user should receive an appropriate error message.

The system may retry the request where appropriate.

---

# 52. Notification Requirements

The application should provide user-facing status messages for important events.

Examples include:

- PDF Upload Successful
- PDF Processing Started
- PDF Ready
- PDF Processing Failed
- Study Session Started
- Quiz Started
- Quiz Submitted
- Quiz Completed
- AI Service Temporarily Unavailable

---

# 53. PDF Upload Notification

After successful upload:

> Your PDF has been uploaded successfully and is now being processed.

---

# 54. PDF Ready Notification

After successful processing:

> Your PDF is ready. You can now start studying or test your skills.

---

# 55. PDF Processing Failure Notification

If processing fails:

> We could not process this PDF. Please try again later.

---

# 56. Quiz Completion Notification

After quiz submission:

> Your quiz has been submitted successfully.

The user should then be able to view the result.

---

# 57. AI Service Availability Notification

If AI processing is temporarily unavailable:

> AI service is temporarily unavailable. Please try again later.

---

# 58. Notification Delivery

For Version 1, notifications may be displayed directly inside the application UI.

The system does not require email or push notifications for the core Version 1 workflow.

---

# 59. API Request Logging

The backend should maintain appropriate logs for important operations.

Logs may include:

- Request Type
- Endpoint
- User ID, where appropriate
- Resource ID
- Timestamp
- Response Status
- Error Type

Sensitive credentials must never be logged.

---

# 60. Internal Service Logging

The Python AI Service should log important processing events.

Examples:

- PDF Processing Started
- PDF Processing Completed
- OCR Started
- Embedding Generation Started
- Embedding Generation Completed
- RAG Request
- Gemini Request Failure
- Quiz Generation Failure

Logs should not expose API keys or sensitive user information unnecessarily.

---

# 61. API Versioning

The application API should support versioning.

The initial API version should be:

v1

Future API changes should be designed so that breaking changes can be introduced through a new API version.

---

# 62. API Documentation

The final API should be documented.

Documentation should include:

- Endpoint
- HTTP Method
- Authentication Requirement
- Request Parameters
- Request Body
- Response
- Error Responses
- Authorization Rules

The exact API documentation will be created in the API Specification document.

---

# 63. Integration Acceptance Criteria

The API and Integration System will be considered complete when:

- ReactJS communicates with Laravel through APIs.
- Laravel handles authentication and authorization.
- Laravel handles application business logic.
- Laravel communicates with Python AI Service.
- Python AI Service handles AI-related processing.
- Python AI Service communicates with Gemini.
- PostgreSQL stores application data.
- pgvector supports semantic retrieval.
- Cloudflare R2 stores private files.
- PDF processing runs asynchronously.
- PDF processing status can be retrieved.
- Study Mode communicates with the AI pipeline.
- Quiz Mode communicates with the AI pipeline.
- Voice answers can be processed.
- Text answers can be evaluated.
- Quiz submissions can be finalized.
- Quiz attempts are locked after submission.
- API errors are handled consistently.
- AI failures are handled gracefully.
- PDF processing failures are handled gracefully.
- Temporary failures can be retried where appropriate.
- Duplicate operations are controlled.
- User-friendly notifications are displayed.
- Sensitive internal errors are not exposed.
- API keys remain server-side.
- API versioning is supported.

---

# 64. Locked Integration Decisions

| Decision               | Requirement                       |
| ---------------------- | --------------------------------- |
| Frontend               | ReactJS                           |
| Main Backend           | Laravel                           |
| AI Service             | Python                            |
| Database               | PostgreSQL                        |
| Vector Search          | pgvector                          |
| File Storage           | Cloudflare R2                     |
| AI Provider            | Gemini API                        |
| Frontend Communication | ReactJS → Laravel API             |
| AI Communication       | Laravel → Python AI Service       |
| Gemini Communication   | Python AI Service → Gemini        |
| PDF Processing         | Asynchronous                      |
| PDF Status             | Processing / Ready / Failed       |
| Quiz Submission        | Finalized and locked after Submit |
| Skipped Questions      | Unattempted, 0 marks              |
| Negative Marking       | None                              |
| API Version            | v1                                |
| Notifications          | In-app for Version 1              |
| API Keys               | Server-side only                  |

---

End of SRS Part 7
