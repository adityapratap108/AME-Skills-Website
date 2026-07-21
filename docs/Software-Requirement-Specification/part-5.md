# 01_Software_Requirement_Specification.md

## Part 5 - Dashboard, Progress, Study Time, Quiz History and Reports

**Project Name:** AME AI Tutor

**Document ID:** SRS-01-Part-5

**Version:** 1.0

**Status:** Draft

---

# 1. Dashboard Overview

The system should provide each user with a personal dashboard.

The dashboard should provide a clear overview of:

- User Information
- Uploaded PDFs
- PDF Upload Dates
- PDF Processing Status
- Study Usage
- Quiz Usage
- Study Time
- Quiz Scores
- Topic Progress
- Overall Progress

The dashboard should allow the user to understand their learning activity from one place.

---

# 2. User Profile Information

## FR-100 - Display User Information

The dashboard should display basic information about the logged-in user.

The information may include:

- User Name
- Email Address
- Profile Picture, if available
- Account Creation Date

The dashboard should not expose sensitive authentication information.

---

# 3. Uploaded PDF List

## FR-101 - Display Uploaded PDFs

The dashboard should display the PDFs uploaded by the user.

Each PDF entry should show:

- PDF Name
- Upload Date
- File Size
- Number of Pages, where available
- Processing Status
- Study Progress
- Quiz Progress

Example:

| PDF                             | Upload Date  | Status     | Study Progress | Quiz Progress |
| ------------------------------- | ------------ | ---------- | -------------- | ------------- |
| Aircraft Electrical Systems.pdf | 21 July 2026 | Ready      | 80%            | 60%           |
| Aircraft Engines.pdf            | 20 July 2026 | Processing | -              | -             |

---

# 4. PDF Processing Status

## FR-102 - Show PDF Processing Status

The dashboard should show the current processing state of each uploaded PDF.

Possible user-facing statuses:

- Uploading
- Processing
- Ready
- Failed

The detailed internal processing stages may be stored by the system but do not need to be displayed to the user.

If processing fails, the user should be informed that the PDF could not be processed.

---

# 5. Open PDF

## FR-103 - Open PDF Workspace

The user should be able to open a processed PDF from the dashboard.

When the user opens a PDF, the system should provide access to:

- PDF Context
- Study Mode
- Quiz Mode
- Study Progress
- Quiz Progress
- Previous Activity

---

# 6. PDF Context on Dashboard

The dashboard or PDF workspace should show basic information about the selected PDF.

The information should include:

- PDF Name
- Number of Pages
- Number of Topics
- Upload Date
- Processing Status
- Study Progress
- Quiz Progress

---

# 7. Study Usage Tracking

## FR-104 - Track Study Usage

The system should track when a user uses a PDF for Study Mode.

The system should record:

- PDF ID
- User ID
- Study Session ID
- Start Time
- End Time
- Study Duration
- Topics Studied
- Topics Completed

---

# 8. Study Time Tracking

## FR-105 - Track Study Time

The system should calculate how much time the user has spent studying a specific PDF.

Example:

PDF:

Aircraft Electrical Systems

Total Study Time:

2 Hours 15 Minutes

The study time should be calculated from valid Study Mode sessions.

---

# 9. Study Session Tracking

Each Study Mode session should contain:

- Session ID
- User ID
- PDF ID
- Start Time
- End Time
- Duration
- Current Topic
- Completed Topics
- Session Status

Session status may be:

- Active
- Completed
- Interrupted

---

# 10. Study Progress

## FR-106 - Display Study Progress

The system should show the user's Study Mode progress for each PDF.

Progress should be based on completed topics.

Example:

Total Topics: 10

Completed Topics: 7

Study Progress: 70%

---

# 11. Topic-Level Study Progress

The system should display topic-level progress.

Example:

| Topic                   | Status      |
| ----------------------- | ----------- |
| Electrical Fundamentals | Completed   |
| Batteries               | Completed   |
| Generators              | Completed   |
| Alternators             | In Progress |
| Distribution Systems    | Not Started |

---

# 12. Quiz Usage Tracking

## FR-107 - Track Quiz Usage

The system should track when a user uses a PDF for Quiz Mode.

The system should record:

- PDF ID
- User ID
- Quiz Session ID
- Start Time
- End Time
- Submission Time
- Topics Attempted
- Questions Attempted
- Questions Skipped
- Score

---

# 13. Quiz Session Tracking

Each Quiz Mode session should contain:

- Session ID
- User ID
- PDF ID
- Start Time
- End Time
- Submission Time
- Current Topic
- Current Question
- Completed Questions
- Unattempted Questions
- Score
- Session Status

Session status may be:

- Active
- In Progress
- Submitted
- Completed

---

# 14. Quiz Progress

## FR-108 - Display Quiz Progress

The system should show Quiz Mode progress for each PDF.

Progress should include:

- Topics Completed
- Total Topics
- Questions Attempted
- Questions Skipped
- Current Topic
- Overall Quiz Progress

Example:

Quiz Progress:

Topics Completed: 4 / 10

Questions Attempted: 35

Questions Skipped: 3

Overall Progress: 40%

---

# 15. Topic-Level Quiz Progress

The system should display the quiz status for each topic.

Example:

| Topic                   | Status      | Score |
| ----------------------- | ----------- | ----- |
| Electrical Fundamentals | Completed   | 85%   |
| Batteries               | Completed   | 90%   |
| Generators              | Completed   | 75%   |
| Alternators             | In Progress | -     |
| Distribution Systems    | Not Started | -     |

---

# 16. Quiz Score Tracking

## FR-109 - Store Quiz Scores

The system should store scores for:

- Individual Questions
- Individual Topics
- Complete Quiz Attempts

The system should not use negative marking.

Skipped questions should receive zero marks.

---

# 17. Topic Score History

The system should maintain the score obtained by the user for each completed topic.

Example:

Topic:

Generator Systems

Attempt 1:

65%

Attempt 2:

85%

The system should preserve completed quiz attempt history.

---

# 18. Overall Quiz Score

## FR-110 - Display Overall Quiz Score

After the final quiz submission, the system should calculate and display the overall score.

Example:

Overall Score:

82%

Status:

Passed

The passing score is 75%.

---

# 19. Quiz Pass/Fail Status

The system should display:

- Passed
- Not Passed

Rules:

Score >= 75%

Result: Passed

Score < 75%

Result: Not Passed

---

# 20. Unattempted Questions

## FR-111 - Track Unattempted Questions

The system should track questions that were skipped.

A skipped question is considered:

Unattempted

The score for the question is:

0 marks

No negative marks will be applied.

The final report should show the total number of unattempted questions.

---

# 21. Quiz Attempt History

## FR-112 - Store Quiz Attempt History

The system should maintain a history of completed quiz attempts.

Each attempt should contain:

- Attempt ID
- PDF
- Attempt Date
- Start Time
- Submission Time
- Total Questions
- Correct Answers
- Incorrect Answers
- Unattempted Questions
- Score
- Pass/Fail Status

Example:

| Attempt   | Date         | Score | Status     |
| --------- | ------------ | ----- | ---------- |
| Attempt 1 | 21 July 2026 | 68%   | Not Passed |
| Attempt 2 | 22 July 2026 | 82%   | Passed     |

---

# 22. Study History

## FR-113 - Store Study History

The system should maintain a history of Study Mode activity.

Each study activity should contain:

- PDF
- Date
- Study Duration
- Topics Studied
- Topics Completed

Example:

| PDF                         | Date         | Study Time |
| --------------------------- | ------------ | ---------- |
| Aircraft Electrical Systems | 21 July 2026 | 45 Minutes |
| Aircraft Electrical Systems | 22 July 2026 | 30 Minutes |

---

# 23. PDF Activity Summary

## FR-114 - Display PDF Activity Summary

For each PDF, the system should provide a summary of the user's activity.

The summary should include:

- Upload Date
- Total Study Time
- Study Progress
- Quiz Attempts
- Best Quiz Score
- Latest Quiz Score
- Quiz Status
- Topic Progress

Example:

Aircraft Electrical Systems

Uploaded:

21 July 2026

Study Time:

2 Hours 15 Minutes

Study Progress:

100%

Quiz Attempts:

2

Best Score:

85%

Latest Score:

82%

Status:

Passed

---

# 24. PDF Usage Type

The system should track how the user used each PDF.

Possible usage types:

- Study
- Quiz
- Study and Quiz

Example:

PDF:

Aircraft Electrical Systems

Used For:

Study and Quiz

---

# 25. Total User Study Time

## FR-115 - Calculate Total Study Time

The system should calculate the user's total Study Mode time across all PDFs.

Example:

Total Study Time:

15 Hours 30 Minutes

This value should be calculated from recorded Study Sessions.

---

# 26. PDF-Specific Study Time

The system should separately track study time for each PDF.

Example:

Aircraft Electrical Systems:

2 Hours 15 Minutes

Aircraft Engines:

4 Hours 30 Minutes

Aircraft Materials:

1 Hour 45 Minutes

---

# 27. Dashboard Summary

The main dashboard should provide a summary of the user's activity.

The summary may include:

- Total Uploaded PDFs
- PDFs Ready
- PDFs Processing
- Total Study Time
- PDFs Studied
- PDFs Tested
- Completed Topics
- Quiz Attempts
- Best Quiz Score

The dashboard should prioritize information related to the user's uploaded PDFs and learning activity.

---

# 28. PDF Dashboard Card

Each PDF may be represented using a card.

The card should display:

PDF Name

Upload Date

Processing Status

Study Progress

Quiz Progress

Total Study Time

Latest Quiz Score

Example:

Aircraft Electrical Systems

Uploaded: 21 July 2026

Status: Ready

Study Progress: 80%

Quiz Progress: 60%

Study Time: 2 Hours 15 Minutes

Latest Quiz Score: 78%

---

# 29. PDF Detail Page

When the user selects a PDF, the system should show a dedicated PDF workspace.

The workspace should provide:

- PDF Information
- PDF Context
- Study Mode
- Quiz Mode
- Study Progress
- Quiz Progress
- Study Time
- Quiz History
- Topic Scores

---

# 30. Topic Performance

## FR-116 - Display Topic Performance

The system should show performance for individual topics.

Example:

| Topic                   | Study Status | Quiz Score |
| ----------------------- | ------------ | ---------- |
| Electrical Fundamentals | Completed    | 90%        |
| Batteries               | Completed    | 85%        |
| Generators              | Completed    | 75%        |
| Alternators             | In Progress  | -          |

---

# 31. Strong and Weak Topics

## FR-117 - Identify Topic Performance

After a quiz is completed, the system should identify topics based on their scores.

Strong topics are topics where the user has performed well.

Weak topics are topics where the user has performed poorly.

Example:

Strong Topics:

- Batteries - 95%
- Generators - 90%

Needs Improvement:

- Circuit Protection - 55%
- Distribution Systems - 60%

The system should base this information on quiz performance.

---

# 32. Final Quiz Report

## FR-118 - Display Final Quiz Report

The final quiz report should display:

- PDF Name
- Attempt Date
- Overall Score
- Pass/Fail Status
- Total Questions
- Correct Answers
- Incorrect Answers
- Unattempted Questions
- Topic-wise Scores
- Strong Topics
- Weak Topics
- Quiz Completion Status

---

# 33. Study Completion Report

## FR-119 - Display Study Completion Report

After completing all topics in Study Mode, the system should display:

- PDF Name
- Total Topics
- Completed Topics
- Total Study Time
- Completion Percentage
- Completion Status

Example:

Study Completed

PDF:

Aircraft Electrical Systems

Topics Completed:

10 / 10

Study Time:

2 Hours 15 Minutes

Completion:

100%

---

# 34. Progress Persistence

## FR-120 - Save Progress

The system should persist the user's progress in the database.

Progress should not be lost when:

- User logs out.
- User closes the browser.
- User returns later.
- User changes device.

The user should be able to continue from the last saved state.

---

# 35. Progress Independence

Study Mode and Quiz Mode progress must remain independent.

Example:

Study Progress:

100%

Quiz Progress:

60%

The system should not automatically mark Quiz Mode as completed because Study Mode is completed.

Similarly, completing Quiz Mode should not automatically mark Study Mode as completed.

---

# 36. Time Tracking Rules

The system should track Study Mode time accurately.

The system should avoid counting unlimited idle time as active study time where technically possible.

The system should track active Study Mode sessions.

If the user leaves the Study Mode session, the session should be paused or ended according to the application's session handling logic.

The exact idle timeout value will be defined during technical implementation.

---

# 37. Quiz Time Tracking

The system may record:

- Quiz Start Time
- Quiz End Time
- Quiz Duration
- Submission Time

Quiz duration should be stored separately from Study Mode time.

Quiz duration should not be included in the user's Study Time.

---

# 38. User Activity Data

The system should maintain user activity data required for:

- Dashboard
- Study Progress
- Quiz Progress
- Study Time
- Quiz History
- Reports

The system should associate all activity with:

- User ID
- PDF ID

Where applicable, activity should also be associated with:

- Topic ID
- Session ID
- Attempt ID

---

# 39. Data Consistency

The dashboard should always display data based on stored database records.

The system should not calculate important historical data only on the frontend.

Important values such as:

- Study Time
- Quiz Score
- Topic Score
- Quiz Attempt History
- PDF Progress

should be stored or reliably calculated from persistent backend data.

---

# 40. Dashboard Access

Only authenticated users should be able to access their dashboard.

A user should only be able to see:

- Their own PDFs
- Their own Study History
- Their own Quiz History
- Their own Scores
- Their own Progress

A user must not be able to access another user's learning data.

---

# 41. Acceptance Criteria

The Dashboard and Progress System will be considered complete when:

- User information is displayed.
- Uploaded PDFs are displayed.
- PDF upload dates are displayed.
- PDF processing status is displayed.
- Study progress is displayed.
- Quiz progress is displayed.
- Study time is tracked.
- PDF-specific study time is tracked.
- Total user study time is available.
- Study sessions are stored.
- Quiz sessions are stored.
- Quiz scores are stored.
- Topic-wise scores are stored.
- Quiz attempt history is available.
- Study history is available.
- Unattempted questions are tracked.
- Skipped questions receive zero marks.
- Quiz pass/fail status is displayed.
- 75% is used as the passing score.
- PDF usage type can be identified as Study, Quiz, or Study and Quiz.
- Topic performance is displayed.
- Strong and weak topics are identified from quiz performance.
- Final quiz reports are displayed.
- Study completion reports are displayed.
- Progress persists across sessions.
- Study and Quiz progress remain independent.
- Users can only access their own data.

---

# 42. Locked Decisions Used in This Part

| Decision             | Requirement                                         |
| -------------------- | --------------------------------------------------- |
| Study Time           | Track total and PDF-specific Study Mode time        |
| PDF Usage            | Track whether PDF was used for Study, Quiz, or both |
| Study Progress       | Based on completed topics                           |
| Quiz Progress        | Based on completed topics and quiz activity         |
| Quiz Passing Score   | 75%                                                 |
| Negative Marking     | None                                                |
| Skipped Questions    | Unattempted and receive 0 marks                     |
| Quiz History         | Store completed quiz attempts                       |
| Study History        | Store Study Mode sessions                           |
| Topic Performance    | Display topic-wise quiz scores                      |
| Progress Persistence | Progress must be saved and recoverable              |
| Data Access          | Users can only access their own data                |

---

End of SRS Part 5
