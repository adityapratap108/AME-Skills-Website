# 01_Software_Requirement_Specification.md

## Part 2

**Project Name:** AME AI Tutor

**Document ID:** SRS-01-Part-2

**Version:** 1.1

**Status:** Approved Draft

---

# 1. User Registration Requirements

## FR-016 - User Registration

### Purpose

Allow a new student to create an account.

### Business Logic

A student can register using:

- Full Name
- Email Address
- Password
- Confirm Password

### Validation Rules

- Name is required.
- Email is required.
- Email must be unique.
- Password must contain at least 8 characters.

### Acceptance Criteria

- User account is created successfully.
- Verification email is sent.
- User is redirected to the Login page.

---

## FR-017 - Email Verification

### Purpose

Verify the user's email address.

### Business Logic

- Verification email should be sent immediately after registration.
- User cannot access the dashboard until the email is verified.
- User can request a new verification email if the previous link expires.

### Acceptance Criteria

- Email verification works successfully.
- Expired links display a proper error message.

---

## FR-018 - User Login

### Purpose

Allow existing users to access the application.

### Business Logic

Login using:

- Email
- Password

### Validation Rules

- Email must exist.
- Password must be correct.
- Email must be verified.

### Acceptance Criteria

- User is redirected to the Dashboard after successful login.

---

## FR-019 - Google Login

### Purpose

Allow users to sign in using Google.

### Business Logic

- Authenticate using Google.
- If the account exists, login directly.
- Otherwise create a new account automatically.

### Acceptance Criteria

- User reaches the Dashboard successfully.

---

## FR-020 - Forgot Password

### Purpose

Allow users to reset their password.

### Business Logic

- User enters registered email.
- Password reset link is sent.
- User creates a new password.

### Acceptance Criteria

- Previous password becomes invalid immediately.

---

# 2. User Profile Requirements

## FR-021 - View Profile

The user should be able to view:

- Name
- Email
- Profile Photo
- Registration Date
- Total Uploaded PDFs
- Total Study Time
- Total Quiz Time

---

## FR-022 - Edit Profile

The user can update:

- Name
- Profile Photo

The email address cannot be changed in Version 1.

---

# 3. Dashboard Requirements

## FR-023 - Dashboard Overview

The Dashboard should display:

- Welcome Message
- User Name
- Total Uploaded PDFs
- Total Study Time
- Total Quiz Time
- Average Quiz Score
- Completed Topics
- Pending Topics

---

## FR-024 - Recent PDFs

Each PDF card should display:

- PDF Name
- Upload Date
- Total Pages
- Current Status
- Last Opened Date

---

## FR-025 - Recent Activities

The Dashboard should display recent activities:

- PDF Uploaded
- Study Started
- Quiz Started
- Quiz Completed
- Study Completed

---

# 4. PDF Upload Requirements

## FR-026 - Upload PDF

### Purpose

Allow students to upload AME study PDFs.

### Supported File Type

- PDF (.pdf)

### Validation Rules

- File must be a PDF.
- Empty PDF is not allowed.
- Password-protected PDF is not allowed.
- Corrupted PDF is not allowed.
- Maximum file size is **100 MB**.

If the uploaded PDF exceeds **100 MB**, the upload must be rejected.

The system should display the following message:

> **This PDF exceeds the maximum allowed size of 100 MB. Please split the PDF into smaller parts and upload them separately.**

### Acceptance Criteria

- PDFs up to 100 MB upload successfully.
- PDFs larger than 100 MB are rejected.
- The user receives a clear guidance message.

---

## FR-027 - Duplicate PDF Detection

### Business Logic

Immediately after upload:

1. Generate SHA-256 Hash.
2. Search existing PDF hashes.
3. If the hash already exists:
   - Reuse existing processed AI data.
   - Do not process the PDF again.
4. Otherwise:
   - Start PDF processing.

### Acceptance Criteria

- Duplicate PDFs are detected using SHA-256 hash.
- Duplicate detection is based on file content, not file name.

---

## FR-028 - PDF Upload Status

Possible upload states:

- Uploading
- Uploaded
- Processing
- Ready
- Failed

---

# 5. PDF Processing Requirements

## FR-029 - PDF Type Detection

The system should automatically detect:

- Digital PDF
- Scanned PDF

---

## FR-030 - Text Extraction

For Digital PDFs, extract:

- Headings
- Paragraphs
- Lists
- Tables

---

## FR-031 - OCR Processing

For Scanned PDFs:

- Run OCR.
- Convert images into readable text.

---

## FR-032 - Image Extraction

The system should extract images separately.

Each extracted image should remain linked to its original topic.

---

## FR-033 - Table Extraction

The system should extract tables separately.

Each table should remain linked to its original section.

---

## FR-034 - Topic Detection

Topics should be identified using:

- Headings
- Sub-headings
- Content structure

Topics should **not** be created using page numbers alone.

---

## FR-035 - PDF Summary

After successful processing, generate:

- Short Summary
- Main Topics
- Estimated Study Time

---

# 6. PDF Status Lifecycle

Uploading

↓

Uploaded

↓

Processing

↓

Ready

↓

Study Mode

OR

Quiz Mode

↓

Completed

---

# 7. Business Rules

### BR-001

A user can upload unlimited PDFs.

---

### BR-002

Only one PDF can be uploaded and processed at a time.

If another upload is attempted while processing is in progress, display:

> **Another PDF is currently being processed. Please wait until it finishes.**

---

### BR-003

Duplicate PDFs must not be processed again.

---

### BR-004

Study Mode is available only after successful processing.

---

### BR-005

Quiz Mode is available only after successful processing.

---

# 8. Error Handling

Display clear error messages for:

- Unsupported File Type
- Corrupted PDF
- Password Protected PDF
- PDF Larger Than 100 MB
- Upload Failed
- Processing Failed
- Internet Connection Lost

All error messages should clearly explain the issue and, where possible, guide the user on how to resolve it.

---

# 9. Acceptance Criteria

The module will be considered complete when:

- User Registration works.
- Login works.
- Google Login works.
- Email Verification works.
- PDF Upload works.
- Duplicate Detection works.
- PDF Processing starts automatically.
- Dashboard updates correctly.
- Upload status changes correctly.
- Files larger than 100 MB are rejected with a proper guidance message.

---

# 10. Locked Decisions

| Decision ID | Value                             |
| ----------- | --------------------------------- |
| PD-006      | Maximum PDF Size = 100 MB         |
| PD-007      | Unlimited PDFs per User           |
| PD-008      | Only 1 Concurrent Upload per User |

---

End of SRS Part 2
