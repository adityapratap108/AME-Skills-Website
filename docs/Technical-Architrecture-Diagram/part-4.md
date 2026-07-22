# 04_Database_Architecture_and_ER_Diagram.md

# Part 4 - Database Architecture and Complete ER Diagram

**Project Name:** AME AI Tutor

**Document ID:** TAD-01-Part-4

**Version:** 1.0

**Status:** Draft

**Depends On:** TAD Part 1, TAD Part 2, TAD Part 3

---

# 1. Purpose

This document defines the complete database architecture for the AME AI Tutor application.

The database must support:

- User accounts
- User profiles
- PDF uploads
- PDF processing
- PDF pages
- PDF sections
- Topics
- Content chunks
- Embeddings
- Images
- Figures
- Study sessions
- Study progress
- Quiz sessions
- Quiz topic progression
- Questions
- Question options
- User answers
- Answer evaluations
- Voice recordings
- Topic scores
- Final quiz scores
- Source references
- PDF usage statistics

The database design must support the complete Version 1 workflow.

---

# 2. Locked Database Technology

The Version 1 database stack is:

**PostgreSQL + pgvector**

PostgreSQL will be the primary relational database.

pgvector will be used for storing and searching AI embeddings.

For Version 1, the project will **not** use MongoDB or MySQL.

The final database architecture is:

```
Laravel
    │
    ▼
PostgreSQL
    │
    ├── Relational Data
    ├── User Data
    ├── PDF Metadata
    ├── PDF Processing Data
    ├── Study Data
    ├── Quiz Data
    └── pgvector Embeddings
```

---

# 3. Database Responsibility

PostgreSQL will store application and structured data.

Cloudflare R2 will store large binary files.

The architecture is:

```
PostgreSQL
    │
    ├── User Information
    ├── PDF Metadata
    ├── PDF Processing Status
    ├── PDF Pages
    ├── PDF Sections
    ├── Topics
    ├── Chunks
    ├── Embeddings
    ├── Quiz Questions
    ├── Answers
    ├── Scores
    └── Progress

Cloudflare R2
    │
    ├── Original PDFs
    ├── Extracted Images
    ├── Extracted Figures
    └── Voice Recordings
```

The database should not store large PDF files or audio files directly.

Instead, PostgreSQL stores the storage key or object path of the file stored in R2.

---

# 4. Database Design Principles

The database must follow these principles:

1. Every user must have a unique ID.

2. Every PDF must belong to a user.

3. Every PDF must have a processing status.

4. Every PDF page must belong to a PDF.

5. Every PDF section must belong to a PDF.

6. Every topic must belong to a PDF.

7. Every content chunk must belong to a PDF.

8. Every content chunk should belong to a topic where applicable.

9. Every embedding must reference a content chunk.

10. Every document asset must reference its PDF.

11. Every study session must belong to a user and PDF.

12. Every quiz session must belong to a user and PDF.

13. Every quiz session must track topic progression.

14. Every question must belong to a quiz session and topic.

15. Every answer must belong to a question and user attempt.

16. Every evaluation must reference the student's answer.

17. Every score must be traceable to its questions.

18. Every source reference must point back to the uploaded PDF.

19. User data must never be mixed between different users.

20. RAG retrieval must be restricted to the selected PDF.

21. Topic-specific RAG retrieval should be restricted to the current topic when required.

22. Laravel remains the source of truth for business and application data.

---

# 5. Entity Overview

The major logical database entities are:

- users
- user_profiles
- pdf_documents
- pdf_processing_jobs
- pdf_pages
- pdf_sections
- topics
- content_chunks
- chunk_embeddings
- document_assets
- study_sessions
- study_progress
- quiz_sessions
- quiz_topic_progress
- questions
- question_options
- user_answers
- answer_evaluations
- voice_recordings
- quiz_topic_scores
- quiz_results
- source_references

---

# 6. High-Level Entity Relationship Overview

The primary relationship structure is:

```
USER
  │
  ├───────────────┐
  │               │
  ▼               ▼
PROFILE       PDF DOCUMENT
                  │
                  ├───────────────┐
                  │               │
                  ▼               ▼
          PROCESSING JOB       PDF PAGE
                                  │
                                  ▼
                             PDF SECTION
                                  │
                                  ▼
                                TOPIC
                                  │
                                  ▼
                            CONTENT CHUNK
                                  │
                                  ▼
                          CHUNK EMBEDDING

PDF DOCUMENT
      │
      ▼
DOCUMENT ASSET
      │
      ├── IMAGE
      ├── FIGURE
      ├── DIAGRAM
      └── OTHER ASSET

USER
  │
  ├───────────────┐
  │               │
  ▼               ▼
STUDY SESSION   QUIZ SESSION
  │               │
  ▼               ▼
STUDY PROGRESS   QUIZ TOPIC PROGRESS
                  │
                  ▼
               QUESTIONS
                  │
                  ▼
            QUESTION OPTIONS
                  │
                  ▼
             USER ANSWERS
                  │
                  ├──────────────┐
                  │              │
                  ▼              ▼
           ANSWER EVALUATION  VOICE RECORDING
                  │
                  ▼
           QUIZ TOPIC SCORE
                  │
                  ▼
              QUIZ RESULT
```

Source references connect AI-generated content back to:

- PDF
- Page
- Section
- Topic
- Chunk
- Asset

---

# 7. Users Table

**Table Name:** `users`

### Purpose

Stores authentication and basic account information.

### Recommended Fields

- `id`
- `name`
- `email`
- `password`
- `email_verified_at`
- `remember_token`
- `created_at`
- `updated_at`

The Laravel authentication system will use this table.

---

# 8. User Profiles Table

**Table Name:** `user_profiles`

### Purpose

Stores additional student information.

### Recommended Fields

- `id`
- `user_id`
- `profile_photo_path`
- `phone`
- `institution`
- `course`
- `registration_number`
- `created_at`
- `updated_at`

Not every field is mandatory.

The exact fields can be adjusted according to the client's requirements.

---

# 9. User and Profile Relationship

Relationship:

```
users
  │
  │ 1 : 1
  ▼
user_profiles
```

One user has one profile.

Foreign key:

```
user_profiles.user_id
```

References:

```
users.id
```

`user_profiles.user_id` should be unique so that one user cannot have multiple profile rows.

---

# 10. PDF Documents Table

**Table Name:** `pdf_documents`

### Purpose

Stores metadata about uploaded PDFs.

### Recommended Fields

- `id`
- `user_id`
- `title`
- `original_filename`
- `storage_key`
- `file_size`
- `mime_type`
- `page_count`
- `document_type`
- `processing_status`
- `processing_error`
- `uploaded_at`
- `processed_at`
- `created_at`
- `updated_at`

### Possible Document Types

- `digital`
- `scanned`
- `mixed`
- `unknown`

### Possible Processing Statuses

- `uploaded`
- `queued`
- `processing`
- `ready`
- `failed`

Example:

```
id = 123
user_id = 5
title = Aircraft Hydraulic Systems
original_filename = hydraulic_systems.pdf
file_size = 5242880
page_count = 85
document_type = mixed
processing_status = ready
```

---

# 11. User and PDF Relationship

Relationship:

```
users
  │
  │ 1 : N
  ▼
pdf_documents
```

One user can upload multiple PDFs.

Every PDF belongs to exactly one user.

Example:

```
User 1
  ├── PDF A
  ├── PDF B
  └── PDF C
```

Foreign key:

```
pdf_documents.user_id
```

References:

```
users.id
```

---

# 12. PDF Processing Jobs Table

**Table Name:** `pdf_processing_jobs`

### Purpose

Tracks individual PDF processing operations.

### Recommended Fields

- `id`
- `pdf_document_id`
- `job_type`
- `status`
- `started_at`
- `completed_at`
- `error_message`
- `attempts`
- `created_at`
- `updated_at`

### Possible Job Types

- `full_processing`
- `ocr`
- `chunking`
- `embedding`

### Possible Statuses

- `queued`
- `processing`
- `completed`
- `failed`

This table helps track the processing lifecycle and failures.

---

# 13. PDF and Processing Job Relationship

Relationship:

```
pdf_documents
      │
      │ 1 : N
      ▼
pdf_processing_jobs
```

One PDF may have multiple processing jobs.

Example:

```
PDF
  ├── Full Processing Job
  ├── OCR Job
  └── Embedding Job
```

This allows individual processing stages to be tracked.

---

# 14. PDF Pages Table

**Table Name:** `pdf_pages`

### Purpose

Stores page-level processing information.

### Recommended Fields

- `id`
- `pdf_document_id`
- `page_number`
- `width`
- `height`
- `rotation`
- `page_type`
- `extracted_text`
- `normalized_text`
- `ocr_text`
- `ocr_confidence`
- `image_count`
- `table_count`
- `created_at`
- `updated_at`

### Possible Page Types

- `digital`
- `scanned`
- `mixed`
- `image_only`
- `unreadable`

The page number must represent the original PDF page number.

---

# 15. PDF and Page Relationship

Relationship:

```
pdf_documents
      │
      │ 1 : N
      ▼
pdf_pages
```

One PDF has many pages.

Every page belongs to exactly one PDF.

---

# 16. PDF Sections Table

**Table Name:** `pdf_sections`

### Purpose

Stores detected document sections.

### Recommended Fields

- `id`
- `pdf_document_id`
- `parent_section_id`
- `section_number`
- `title`
- `start_page`
- `end_page`
- `section_order`
- `created_at`
- `updated_at`

The `parent_section_id` allows hierarchical sections.

Example:

```
4 Hydraulic Systems

    4.1 Hydraulic Pumps

        4.1.1 Types of Pumps
```

This can be represented as:

```
Section 4
  │
  └── Section 4.1
        │
        └── Section 4.1.1
```

---

# 17. Section Hierarchy

The relationship is:

```
pdf_sections
      │
      ├── parent_section_id = NULL
      │
      ▼
  Main Section
      │
      ▼
  Child Section
      │
      ▼
  Subsection
```

A section can have:

- 0 or 1 parent section
- 0 or many child sections

This allows the PDF's original document structure to be preserved.

---

# 18. Topics Table

**Table Name:** `topics`

### Purpose

Stores AI-detected or structure-detected learning topics.

### Recommended Fields

- `id`
- `pdf_document_id`
- `section_id`
- `name`
- `description`
- `topic_order`
- `start_page`
- `end_page`
- `created_at`
- `updated_at`

Example:

```
Topic:
Hydraulic Pumps

Section:
Hydraulic Systems

Start Page:
40

End Page:
45
```

---

# 19. PDF and Topic Relationship

Relationship:

```
pdf_documents
      │
      │ 1 : N
      ▼
    topics
```

One PDF can contain multiple topics.

Every topic belongs to one PDF.

---

# 20. Section and Topic Relationship

Relationship:

```
pdf_sections
      │
      │ 1 : N
      ▼
    topics
```

A section can contain multiple topics.

For Version 1, a topic belongs to one primary section.

If future requirements require a topic to belong to multiple sections, a junction table can be introduced later.

---

# 21. Content Chunks Table

**Table Name:** `content_chunks`

### Purpose

Stores semantic chunks used by the RAG pipeline.

### Recommended Fields

- `id`
- `pdf_document_id`
- `topic_id`
- `section_id`
- `page_start`
- `page_end`
- `content_type`
- `content`
- `chunk_order`
- `token_count`
- `metadata`
- `created_at`
- `updated_at`

### Possible Content Types

- `text`
- `table`
- `figure`
- `image`
- `ocr`
- `mixed`

`metadata` should use PostgreSQL JSONB.

Example metadata:

```
{
  "heading": "Hydraulic Pumps",
  "figure_number": "4.2",
  "caption": "Hydraulic System Schematic"
}
```

---

# 22. PDF and Chunk Relationship

Relationship:

```
pdf_documents
      │
      │ 1 : N
      ▼
content_chunks
```

Every chunk must belong to a PDF.

This relationship is essential for document-level RAG isolation.

---

# 23. Topic and Chunk Relationship

Relationship:

```
topics
  │
  │ 1 : N
  ▼
content_chunks
```

One topic can have multiple chunks.

Example:

```
Topic:
Hydraulic Pumps

  ├── Chunk 1
  ├── Chunk 2
  ├── Chunk 3
  └── Chunk 4
```

This allows topic-specific retrieval.

---

# 24. Chunk Embeddings Table

**Table Name:** `chunk_embeddings`

### Purpose

Stores vector embeddings associated with content chunks.

### Recommended Fields

- `id`
- `content_chunk_id`
- `embedding`
- `embedding_model`
- `created_at`
- `updated_at`

The `embedding` column uses the pgvector data type.

The exact vector dimension depends on the selected embedding model.

The embedding dimension must remain consistent for all vectors used in the same vector index.

---

# 25. Chunk and Embedding Relationship

Relationship:

```
content_chunks
      │
      │ 1 : 1
      ▼
chunk_embeddings
```

For Version 1, each content chunk has one active embedding.

If the embedding model changes later, the architecture can be expanded to support multiple embeddings per chunk.

---

# 26. Document Assets Table

**Table Name:** `document_assets`

### Purpose

Stores metadata for extracted images, figures, diagrams, and other document assets.

### Recommended Fields

- `id`
- `pdf_document_id`
- `pdf_page_id`
- `topic_id`
- `asset_type`
- `storage_key`
- `original_name`
- `caption`
- `figure_number`
- `width`
- `height`
- `metadata`
- `created_at`
- `updated_at`

### Possible Asset Types

- `image`
- `figure`
- `diagram`
- `chart`
- `schematic`
- `table_image`

The actual asset file is stored in Cloudflare R2.

PostgreSQL stores the metadata and storage key.

---

# 27. PDF and Document Assets Relationship

Relationship:

```
pdf_documents
      │
      │ 1 : N
      ▼
document_assets
```

One PDF can contain multiple assets.

Example:

```
PDF
  ├── Figure 4.1
  ├── Figure 4.2
  ├── Diagram 5.1
  └── Image 6.2
```

---

# 28. PDF Page and Asset Relationship

Relationship:

```
pdf_pages
      │
      │ 1 : N
      ▼
document_assets
```

An asset can be associated with the page from which it was extracted.

This is important when the user asks:

```
"Explain Figure 4.2"
```

The system can identify:

- The figure number
- The source PDF
- The page number
- The image file
- The surrounding text
- The related topic

---

# 29. Study Sessions Table

**Table Name:** `study_sessions`

### Purpose

Tracks when a user studies a PDF.

### Recommended Fields

- `id`
- `user_id`
- `pdf_document_id`
- `started_at`
- `ended_at`
- `duration_seconds`
- `status`
- `created_at`
- `updated_at`

### Possible Statuses

- `active`
- `completed`
- `abandoned`

Study duration can be calculated from:

```
ended_at - started_at
```

or maintained by the application when a session is closed.

---

# 30. User and Study Session Relationship

Relationship:

```
users
  │
  │ 1 : N
  ▼
study_sessions
```

A user can have multiple study sessions.

---

# 31. PDF and Study Session Relationship

Relationship:

```
pdf_documents
      │
      │ 1 : N
      ▼
study_sessions
```

A PDF can be studied multiple times by the same user.

---

# 32. Study Progress Table

**Table Name:** `study_progress`

### Purpose

Tracks the user's progress through topics while studying.

### Recommended Fields

- `id`
- `study_session_id`
- `topic_id`
- `started_at`
- `completed_at`
- `duration_seconds`
- `status`
- `created_at`
- `updated_at`

### Possible Statuses

- `not_started`
- `in_progress`
- `completed`

This supports topic-by-topic study tracking.

---

# 33. Study Session and Progress Relationship

Relationship:

```
study_sessions
      │
      │ 1 : N
      ▼
study_progress
```

One study session can contain multiple topic progress records.

---

# 34. Topic and Study Progress Relationship

Relationship:

```
topics
  │
  │ 1 : N
  ▼
study_progress
```

A topic can be studied multiple times across different sessions.

---

# 35. Quiz Sessions Table

**Table Name:** `quiz_sessions`

### Purpose

Represents one complete testing session.

### Recommended Fields

- `id`
- `user_id`
- `pdf_document_id`
- `started_at`
- `submitted_at`
- `total_questions`
- `attempted_questions`
- `skipped_questions`
- `total_score`
- `max_score`
- `percentage`
- `passing_score`
- `result_status`
- `created_at`
- `updated_at`

For Version 1:

```
passing_score = 75
```

### Possible Result Statuses

- `in_progress`
- `passed`
- `failed`

No negative marking is used.

---

# 36. Quiz Session and User Relationship

Relationship:

```
users
  │
  │ 1 : N
  ▼
quiz_sessions
```

One user can take multiple quizzes.

---

# 37. Quiz Session and PDF Relationship

Relationship:

```
pdf_documents
      │
      │ 1 : N
      ▼
quiz_sessions
```

A PDF can be used for multiple quiz sessions.

---

# 38. Quiz Topic Progress Table

**Table Name:** `quiz_topic_progress`

### Purpose

Tracks topic-by-topic quiz progression.

### Recommended Fields

- `id`
- `quiz_session_id`
- `topic_id`
- `topic_order`
- `started_at`
- `completed_at`
- `total_questions`
- `attempted_questions`
- `skipped_questions`
- `score`
- `max_score`
- `percentage`
- `status`
- `created_at`
- `updated_at`

### Possible Statuses

- `not_started`
- `in_progress`
- `completed`

---

# 39. Quiz Topic Progress Logic

The quiz must proceed topic-by-topic.

Example:

```
Quiz Session
     │
     ▼
  Topic 1
     │
     ▼
Complete Topic 1
     │
     ▼
  Topic 2
     │
     ▼
Complete Topic 2
     │
     ▼
  Topic 3
     │
     ▼
Complete Topic 3
```

The system should not ask all topics at once.

The current topic is selected first.

Questions are generated or retrieved for the current topic.

After the topic is completed, the system moves to the next topic.

---

# 40. Questions Table

**Table Name:** `questions`

### Purpose

Stores quiz questions.

### Recommended Fields

- `id`
- `quiz_session_id`
- `quiz_topic_progress_id`
- `topic_id`
- `question_type`
- `question_text`
- `difficulty`
- `marks`
- `question_order`
- `correct_answer_data`
- `explanation`
- `created_at`
- `updated_at`

### Possible Question Types

- `single_choice`
- `multiple_choice`
- `text`
- `voice`

### Possible Difficulty Levels

- `easy`
- `medium`
- `hard`

Every question must belong to a topic.

---

# 41. Question Difficulty Distribution

The Version 1 requirement is:

**Easy and Medium questions must be greater than Hard questions.**

The target distribution for 10 questions is:

```
Easy   = 3
Medium = 4
Hard   = 3
```

The AI question generation system should attempt to follow the configured distribution.

The backend should validate the generated question set before saving it.

The exact number of questions is topic-dependent.

The AI should generate enough questions to adequately cover the particular topic without unnecessarily generating a very large number of questions.

---

# 42. Question Options Table

**Table Name:** `question_options`

### Purpose

Stores options for single-choice and multiple-choice questions.

### Recommended Fields

- `id`
- `question_id`
- `option_text`
- `option_order`
- `is_correct`
- `created_at`
- `updated_at`

This table is required for:

- `single_choice`
- `multiple_choice`

Text and voice questions do not require options.

---

# 43. Question and Options Relationship

Relationship:

```
questions
    │
    │ 1 : N
    ▼
question_options
```

One question can have multiple options.

Example:

```
Question:
What is the purpose of a hydraulic pump?

  A. Option 1
  B. Option 2
  C. Option 3
  D. Option 4
```

---

# 44. User Answers Table

**Table Name:** `user_answers`

### Purpose

Stores the user's submitted answer for each question.

### Recommended Fields

- `id`
- `quiz_session_id`
- `question_id`
- `user_id`
- `answer_type`
- `answer_text`
- `selected_options`
- `is_skipped`
- `submitted_at`
- `created_at`
- `updated_at`

### Possible Answer Types

- `single_choice`
- `multiple_choice`
- `text`
- `voice`

`selected_options` can use PostgreSQL JSONB.

Example:

```
{
  "option_ids": [2, 4]
}
```

---

# 45. Skip Rule

The Version 1 rule is:

**Skip = Unattempted**

If the user skips a question:

```
is_skipped = true
```

The score is:

```
0
```

There is no negative marking.

The user can attempt or change the answer until the final Submit button is pressed.

After final submission:

- The quiz becomes submitted.
- The answers become final.
- The score is calculated.
- The user cannot modify that submitted quiz attempt.

---

# 46. Answer Evaluation Table

**Table Name:** `answer_evaluations`

### Purpose

Stores AI or system-based evaluation results.

### Recommended Fields

- `id`
- `user_answer_id`
- `score`
- `max_score`
- `is_correct`
- `feedback`
- `missing_points`
- `evaluation_method`
- `evaluated_at`
- `created_at`
- `updated_at`

### Possible Evaluation Methods

- `automatic`
- `ai`
- `manual`

For Version 1:

```
Single Choice → Automatic

Multiple Choice → Automatic

Text → AI

Voice → Speech-to-Text + AI
```

---

# 47. Voice Recordings Table

**Table Name:** `voice_recordings`

### Purpose

Stores metadata about voice answers.

### Recommended Fields

- `id`
- `user_answer_id`
- `storage_key`
- `mime_type`
- `file_size`
- `duration_seconds`
- `transcript`
- `transcription_status`
- `created_at`
- `updated_at`

The actual audio file is stored in Cloudflare R2.

### Possible Transcription Statuses

- `pending`
- `processing`
- `completed`
- `failed`

---

# 48. User Answer and Voice Recording Relationship

Relationship:

```
user_answers
      │
      │ 1 : 0..1
      ▼
voice_recordings
```

Only voice-based answers have a voice recording.

---

# 49. Quiz Topic Scores Table

**Table Name:** `quiz_topic_scores`

### Purpose

Stores the final score for each topic in a quiz.

### Recommended Fields

- `id`
- `quiz_session_id`
- `topic_id`
- `total_questions`
- `attempted_questions`
- `skipped_questions`
- `score`
- `max_score`
- `percentage`
- `created_at`
- `updated_at`

Example:

```
Topic:
Hydraulic Pumps

Score:
8 / 10

Percentage:
80%
```

---

# 50. Quiz Result Table

**Table Name:** `quiz_results`

### Purpose

Stores the final result of a completed quiz session.

### Recommended Fields

- `id`
- `quiz_session_id`
- `total_questions`
- `attempted_questions`
- `skipped_questions`
- `total_score`
- `max_score`
- `percentage`
- `passing_score`
- `result_status`
- `completed_at`
- `created_at`
- `updated_at`

Example:

```
Total Questions = 50

Score = 42

Max Score = 50

Percentage = 84%

Passing Score = 75%

Result = Passed
```

---

# 51. Quiz Session and Quiz Result Relationship

Relationship:

```
quiz_sessions
      │
      │ 1 : 0..1
      ▼
quiz_results
```

A quiz session has one final result after submission.

Before submission, the quiz result is not finalized.

---

# 52. Source References Table

**Table Name:** `source_references`

### Purpose

Stores references used to show where AI-generated content came from.

### Recommended Fields

- `id`
- `pdf_document_id`
- `pdf_page_id`
- `section_id`
- `topic_id`
- `content_chunk_id`
- `document_asset_id`
- `reference_type`
- `created_at`

### Possible Reference Types

- `text`
- `table`
- `figure`
- `image`
- `source`

Not every foreign key must be populated.

Example:

```
PDF:
Aircraft Hydraulic Systems

Page:
42

Section:
Hydraulic Systems

Topic:
Hydraulic Pumps

Chunk:
102
```

The frontend can display this information as the source of the explanation or answer.

---

# 53. Source Reference Usage

Example AI response:

```
Hydraulic pumps generate hydraulic pressure by converting mechanical energy into hydraulic energy.

Source:
Page 42
Section: Hydraulic Systems
Topic: Hydraulic Pumps
```

If the answer references a figure, the source can also contain:

```
Figure 4.2
```

This allows the frontend to show:

- Source page
- Section
- Topic
- Figure number
- Related image

---

# 54. Complete Logical ER Diagram

The following diagram represents the complete Version 1 logical database architecture.

```
┌──────────────────┐
│      USERS       │
├──────────────────┤
│ id PK            │
│ name             │
│ email            │
│ password         │
└────────┬─────────┘
         │
         │ 1:1
         ▼
┌──────────────────┐
│  USER_PROFILES   │
├──────────────────┤
│ id PK            │
│ user_id FK       │
│ institution      │
│ course           │
└──────────────────┘


USERS
  │
  │ 1:N
  ▼
┌──────────────────────┐
│   PDF_DOCUMENTS      │
├──────────────────────┤
│ id PK                │
│ user_id FK           │
│ title                │
│ storage_key          │
│ document_type        │
│ processing_status    │
└───────┬──────────────┘
        │
        ├───────────────┐
        │               │
        │ 1:N           │ 1:N
        ▼               ▼
┌───────────────┐   ┌─────────────────────┐
│   PDF_PAGES   │   │ PDF_PROCESSING_JOBS │
├───────────────┤   ├─────────────────────┤
│ id PK         │   │ id PK               │
│ pdf_id FK     │   │ pdf_id FK           │
│ page_number   │   │ job_type            │
│ page_type     │   │ status              │
│ extracted_text│   │ error_message       │
└───────┬───────┘   └─────────────────────┘
        │
        │ 1:N
        ▼
┌──────────────────────┐
│  DOCUMENT_ASSETS     │
├──────────────────────┤
│ id PK                │
│ pdf_id FK            │
│ page_id FK           │
│ topic_id FK          │
│ asset_type           │
│ storage_key          │
│ caption              │
│ figure_number        │
└──────────────────────┘


PDF_DOCUMENTS
        │
        │ 1:N
        ▼
┌──────────────────────┐
│    PDF_SECTIONS      │
├──────────────────────┤
│ id PK                │
│ pdf_id FK            │
│ parent_section_id FK │
│ title                │
│ start_page           │
│ end_page             │
└──────────┬───────────┘
           │
           │ 1:N
           ▼
┌──────────────────────┐
│       TOPICS         │
├──────────────────────┤
│ id PK                │
│ pdf_id FK            │
│ section_id FK        │
│ name                 │
│ topic_order          │
│ start_page           │
│ end_page             │
└──────────┬───────────┘
           │
           │ 1:N
           ▼
┌──────────────────────┐
│   CONTENT_CHUNKS     │
├──────────────────────┤
│ id PK                │
│ pdf_id FK            │
│ topic_id FK          │
│ section_id FK        │
│ page_start           │
│ page_end             │
│ content_type         │
│ content              │
│ metadata JSONB       │
└──────────┬───────────┘
           │
           │ 1:1
           ▼
┌──────────────────────┐
│   CHUNK_EMBEDDINGS   │
├──────────────────────┤
│ id PK                │
│ chunk_id FK          │
│ embedding VECTOR     │
│ embedding_model      │
└──────────────────────┘


USERS
  │
  │ 1:N
  ▼
┌──────────────────────┐
│   STUDY_SESSIONS     │
├──────────────────────┤
│ id PK                │
│ user_id FK           │
│ pdf_id FK            │
│ started_at           │
│ ended_at             │
│ duration_seconds     │
└──────────┬───────────┘
           │
           │ 1:N
           ▼
┌──────────────────────┐
│    STUDY_PROGRESS    │
├──────────────────────┤
│ id PK                │
│ session_id FK        │
│ topic_id FK          │
│ duration_seconds     │
│ status               │
└──────────────────────┘


USERS
  │
  │ 1:N
  ▼
┌──────────────────────┐
│    QUIZ_SESSIONS     │
├──────────────────────┤
│ id PK                │
│ user_id FK           │
│ pdf_id FK            │
│ total_questions      │
│ total_score          │
│ percentage           │
│ result_status        │
└──────────┬───────────┘
           │
           ├─────────────────────┐
           │                     │
           │ 1:N                 │ 1:0..1
           ▼                     ▼
┌──────────────────────┐   ┌──────────────────────┐
│ QUIZ_TOPIC_PROGRESS  │   │    QUIZ_RESULTS      │
├──────────────────────┤   ├──────────────────────┤
│ id PK                │   │ id PK                │
│ quiz_session_id FK   │   │ quiz_session_id FK   │
│ topic_id FK          │   │ total_questions      │
│ topic_order          │   │ total_score          │
│ score                │   │ percentage           │
│ percentage           │   │ result_status        │
│ status               │   └──────────────────────┘
└──────────┬───────────┘
           │
           │ 1:N
           ▼
┌──────────────────────┐
│      QUESTIONS       │
├──────────────────────┤
│ id PK                │
│ quiz_session_id FK   │
│ topic_progress_id FK │
│ topic_id FK          │
│ question_type        │
│ question_text        │
│ difficulty            │
│ marks                │
└──────────┬───────────┘
           │
           ├───────────────┐
           │               │
           │ 1:N           │ 1:N
           ▼               ▼
┌──────────────────┐   ┌──────────────────────┐
│ QUESTION_OPTIONS │   │    USER_ANSWERS      │
├──────────────────┤   ├──────────────────────┤
│ id PK            │   │ id PK                │
│ question_id FK   │   │ question_id FK       │
│ option_text      │   │ quiz_session_id FK   │
│ option_order     │   │ user_id FK           │
│ is_correct       │   │ answer_type          │
└──────────────────┘   │ answer_text          │
                       │ selected_options     │
                       │ is_skipped           │
                       └──────────┬───────────┘
                                  │
                                  │ 1:0..1
                                  ▼
                       ┌──────────────────────┐
                       │  VOICE_RECORDINGS    │
                       ├──────────────────────┤
                       │ id PK                │
                       │ user_answer_id FK    │
                       │ storage_key          │
                       │ transcript            │
                       │ transcription_status  │
                       └──────────────────────┘


USER_ANSWERS
      │
      │ 1:0..1
      ▼
┌────────────────────────┐
│   ANSWER_EVALUATIONS   │
├────────────────────────┤
│ id PK                  │
│ user_answer_id FK      │
│ score                  │
│ max_score              │
│ is_correct             │
│ feedback               │
│ evaluation_method      │
└────────────────────────┘


QUIZ_SESSIONS
      │
      │ 1:N
      ▼
┌────────────────────────┐
│   QUIZ_TOPIC_SCORES    │
├────────────────────────┤
│ id PK                  │
│ quiz_session_id FK     │
│ topic_id FK            │
│ score                  │
│ max_score              │
│ percentage             │
└────────────────────────┘


PDF_DOCUMENTS
      │
      │ 1:N
      ▼
┌────────────────────────┐
│    SOURCE_REFERENCES   │
├────────────────────────┤
│ id PK                  │
│ pdf_id FK              │
│ page_id FK             │
│ section_id FK          │
│ topic_id FK            │
│ chunk_id FK            │
│ asset_id FK            │
│ reference_type         │
└────────────────────────┘
```

---

# 55. Complete Relationship Summary

### User

- hasOne UserProfile
- hasMany PDFDocuments
- hasMany StudySessions
- hasMany QuizSessions

### PDFDocument

- belongsTo User
- hasMany ProcessingJobs
- hasMany PDFPages
- hasMany PDFSections
- hasMany Topics
- hasMany ContentChunks
- hasMany DocumentAssets
- hasMany StudySessions
- hasMany QuizSessions

### PDFSection

- belongsTo PDFDocument
- belongsTo ParentSection
- hasMany ChildSections
- hasMany Topics

### Topic

- belongsTo PDFDocument
- belongsTo PDFSection
- hasMany ContentChunks
- hasMany StudyProgress
- hasMany QuizTopicProgress
- hasMany QuizTopicScores

### ContentChunk

- belongsTo PDFDocument
- belongsTo Topic
- belongsTo PDFSection
- hasOne ChunkEmbedding

### StudySession

- belongsTo User
- belongsTo PDFDocument
- hasMany StudyProgress

### QuizSession

- belongsTo User
- belongsTo PDFDocument
- hasMany QuizTopicProgress
- hasMany Questions
- hasMany UserAnswers
- hasMany QuizTopicScores
- hasOne QuizResult

### Question

- belongsTo QuizSession
- belongsTo QuizTopicProgress
- belongsTo Topic
- hasMany QuestionOptions
- hasMany UserAnswers

### UserAnswer

- belongsTo User
- belongsTo Question
- belongsTo QuizSession
- hasOne VoiceRecording
- hasOne AnswerEvaluation

---

# 56. Foreign Key Rules

Recommended foreign key behavior:

### User to PDF

```
users
  ↓
pdf_documents
```

Recommended:

```
ON DELETE CASCADE
```

If a user is permanently deleted, their PDFs may also be deleted according to the application's data retention policy.

### PDF to Processing Data

```
pdf_documents
  ↓
pdf_pages
pdf_sections
topics
content_chunks
document_assets
pdf_processing_jobs
```

Recommended:

```
ON DELETE CASCADE
```

Deleting a PDF should remove its associated processing data.

### Quiz History

```
quiz_sessions
  ↓
quiz_results
questions
quiz_topic_progress
user_answers
```

Quiz history should be protected from accidental deletion.

The application should explicitly control whether quiz history is deleted when a PDF is deleted.

---

# 57. Database Indexing

Important indexes should include:

- `users.email`
- `pdf_documents.user_id`
- `pdf_documents.processing_status`
- `pdf_documents.created_at`
- `pdf_pages.pdf_document_id`
- `pdf_pages.pdf_document_id + page_number`
- `pdf_sections.pdf_document_id`
- `topics.pdf_document_id`
- `topics.pdf_document_id + topic_order`
- `content_chunks.pdf_document_id`
- `content_chunks.topic_id`
- `content_chunks.pdf_document_id + topic_id`
- `chunk_embeddings.content_chunk_id`
- `study_sessions.user_id`
- `study_sessions.pdf_document_id`
- `quiz_sessions.user_id`
- `quiz_sessions.pdf_document_id`
- `quiz_topic_progress.quiz_session_id`
- `questions.quiz_session_id`
- `questions.topic_id`
- `user_answers.question_id`
- `user_answers.quiz_session_id`
- `source_references.pdf_document_id`

The database should also have an appropriate pgvector index for similarity search.

---

# 58. pgvector Index

The embedding column will be indexed for vector similarity search.

Possible approaches include:

- HNSW
- IVFFlat

For Version 1, HNSW is the preferred starting point.

The exact configuration should be tested with:

- Selected embedding model
- Embedding dimension
- Number of chunks
- Expected query volume
- Production hardware

The final index configuration should be selected during implementation and performance testing.

---

# 59. Document-Level RAG Isolation

Vector search must always include the selected PDF as a filter.

Example:

```
User selects PDF 123
        │
        ▼
  Query Embedding
        │
        ▼
   Vector Search
        │
        ▼
  Filter:
  pdf_document_id = 123
```

This is mandatory.

The system must never retrieve chunks from another PDF during normal Study Mode or Quiz Mode operations.

---

# 60. Topic-Level RAG Isolation

During topic-specific operations:

```
Current PDF = 123

Current Topic = 15
```

The retrieval should use:

```
pdf_document_id = 123

topic_id = 15
```

This ensures that a quiz for Topic 1 does not accidentally retrieve unrelated content from Topic 5.

Broader retrieval should only happen when explicitly required by the AI pipeline.

---

# 61. Quiz Score Calculation

The Version 1 scoring system is:

**No Negative Marking**

For each question:

```
Correct   = Full Marks

Incorrect = 0

Skipped   = 0
```

Final percentage:

```
Percentage =
(Total Score / Maximum Possible Score) × 100
```

Passing score:

```
75%
```

Example:

```
Maximum Score = 100

Student Score = 82

Percentage = 82%

Result = PASS
```

---

# 62. Topic Score Calculation

Each topic has its own score.

Example:

```
Topic 1
8 / 10 = 80%

Topic 2
7 / 10 = 70%

Topic 3
9 / 10 = 90%
```

The dashboard can display:

```
Topic 1 → 80%

Topic 2 → 70%

Topic 3 → 90%
```

The final quiz score is calculated separately from individual topic scores.

---

# 63. Study Time Calculation

The system records:

```
Study Session Start

Study Session End

Duration
```

For topic-level tracking:

```
Topic Start

Topic End

Topic Duration
```

The dashboard can show:

```
Total Study Time:
2 hours 35 minutes

Topic:
Hydraulic Pumps

Study Time:
32 minutes
```

The application should avoid counting the same active session multiple times.

---

# 64. PDF Usage Tracking

The dashboard needs to show:

- PDF Name
- Upload Date
- Total Time Invested
- Usage Type

Usage type:

- Study
- Test

For Study:

- Total Study Time
- Topic Study Progress

For Test:

- Quiz Score
- Topic Scores
- Final Score
- Pass/Fail Status

This information is derived from:

- `pdf_documents`
- `study_sessions`
- `study_progress`
- `quiz_sessions`
- `quiz_topic_scores`
- `quiz_results`

---

# 65. Dashboard Data Flow

The dashboard request flow is:

```
ReactJS
    │
    ▼
Laravel API
    │
    ▼
PostgreSQL
    │
    ├── PDF Documents
    ├── Study Sessions
    ├── Study Progress
    ├── Quiz Sessions
    ├── Quiz Topic Scores
    └── Quiz Results
    │
    ▼
Aggregated Dashboard Response
    │
    ▼
ReactJS Dashboard
```

ReactJS must never directly connect to PostgreSQL.

All database access must happen through the backend.

---

# 66. Example Dashboard Data

The Laravel API may return data logically similar to:

```
{
  "pdf": {
    "id": 123,
    "name": "Aircraft Hydraulic Systems",
    "uploaded_at": "2026-07-20"
  },
  "usage": {
    "total_study_minutes": 120,
    "total_test_attempts": 3
  },
  "latest_quiz": {
    "percentage": 82,
    "status": "passed"
  },
  "topic_scores": [
    {
      "topic": "Hydraulic Pumps",
      "percentage": 80
    },
    {
      "topic": "Hydraulic Valves",
      "percentage": 75
    }
  ]
}
```

The actual API response format will be defined in the API Specification document.

---

# 67. Data Ownership

Every user-owned resource must be checked against the authenticated user.

Example:

```
Authenticated User ID = 5

Requested PDF ID = 123

Check:

pdf_documents.id = 123

AND

pdf_documents.user_id = 5
```

Only then should access be granted.

This must be enforced in Laravel backend authorization.

---

# 68. Security Rule

The frontend must never be trusted for ownership validation.

Incorrect approach:

```
ReactJS sends:

user_id = 5
pdf_id = 123
```

Correct approach:

```
Authenticated Laravel User
        │
        ▼
Laravel gets authenticated user ID
        │
        ▼
Laravel checks PDF ownership
        │
        ▼
Allow / Deny
```

The user ID should come from the authenticated backend authentication context.

---

# 69. Data Lifecycle

The primary Version 1 data lifecycle is:

```
USER CREATED
      │
      ▼
USER UPLOADS PDF
      │
      ▼
PDF RECORD CREATED
      │
      ▼
PDF PROCESSING STARTS
      │
      ▼
PDF CONTENT EXTRACTED
      │
      ▼
TOPICS CREATED
      │
      ▼
CHUNKS CREATED
      │
      ▼
EMBEDDINGS CREATED
      │
      ▼
PDF READY
      │
      ├───────────────┐
      │               │
      ▼               ▼
    STUDY           QUIZ
      │               │
      ▼               ▼
   PROGRESS        QUESTIONS
                      │
                      ▼
                   ANSWERS
                      │
                      ▼
                  EVALUATION
                      │
                      ▼
                 TOPIC SCORES
                      │
                      ▼
                  QUIZ RESULT
```

---

# 70. Database Migration Order

Laravel migrations should be created in dependency order.

Recommended order:

1. `users`

2. `user_profiles`

3. `pdf_documents`

4. `pdf_processing_jobs`

5. `pdf_pages`

6. `pdf_sections`

7. `topics`

8. `content_chunks`

9. `chunk_embeddings`

10. `document_assets`

11. `study_sessions`

12. `study_progress`

13. `quiz_sessions`

14. `quiz_topic_progress`

15. `questions`

16. `question_options`

17. `user_answers`

18. `answer_evaluations`

19. `voice_recordings`

20. `quiz_topic_scores`

21. `quiz_results`

22. `source_references`

This order minimizes foreign key dependency problems.

---

# 71. Recommended Laravel Model Structure

The Laravel application should have models corresponding to the main database entities.

Recommended structure:

```
app/Models/

    User.php

    UserProfile.php

    PdfDocument.php

    PdfProcessingJob.php

    PdfPage.php

    PdfSection.php

    Topic.php

    ContentChunk.php

    ChunkEmbedding.php

    DocumentAsset.php

    StudySession.php

    StudyProgress.php

    QuizSession.php

    QuizTopicProgress.php

    Question.php

    QuestionOption.php

    UserAnswer.php

    AnswerEvaluation.php

    VoiceRecording.php

    QuizTopicScore.php

    QuizResult.php

    SourceReference.php
```

Each model should define its Eloquent relationships.

---

# 72. Example Laravel Relationships

User model:

```
public function pdfDocuments()
{
    return $this->hasMany(PdfDocument::class);
}

public function studySessions()
{
    return $this->hasMany(StudySession::class);
}

public function quizSessions()
{
    return $this->hasMany(QuizSession::class);
}
```

PDF Document model:

```
public function user()
{
    return $this->belongsTo(User::class);
}

public function pages()
{
    return $this->hasMany(PdfPage::class);
}

public function topics()
{
    return $this->hasMany(Topic::class);
}

public function chunks()
{
    return $this->hasMany(ContentChunk::class);
}
```

The complete Laravel relationship implementation will be handled during the backend implementation phase.

---

# 73. Important Database Rule for AI

The AI Service should not directly modify business-critical application data without validation.

Recommended flow:

```
Python AI Service
      │
      ▼
Generate AI Result
      │
      ▼
Laravel Validation
      │
      ▼
PostgreSQL
```

For example, AI-generated questions should be validated before being saved.

The same applies to:

- AI-generated topic data
- AI-generated questions
- AI-generated evaluations
- Scores
- AI-generated metadata

---

# 74. Database and AI Service Separation

The architecture should be:

```
ReactJS
    │
    ▼
Laravel
    │
    ├── PostgreSQL
    │
    ├── Cloudflare R2
    │
    └── Python AI Service
            │
            ├── PDF Processing
            ├── OCR
            ├── Chunking
            ├── Embeddings
            ├── RAG
            └── Gemini
```

The Python Service focuses on AI and document processing.

Laravel remains the source of truth for:

- Users
- PDFs
- Study sessions
- Quiz sessions
- Answers
- Scores
- Progress
- Permissions
- Application state

---

# 75. Final Version 1 Database Architecture

The complete Version 1 architecture is:

```
USERS
  │
  ├── USER_PROFILES
  │
  ├── PDF_DOCUMENTS
  │       │
  │       ├── PDF_PROCESSING_JOBS
  │       ├── PDF_PAGES
  │       ├── PDF_SECTIONS
  │       │       │
  │       │       └── TOPICS
  │       │               │
  │       │               └── CONTENT_CHUNKS
  │       │                       │
  │       │                       └── CHUNK_EMBEDDINGS
  │       │
  │       └── DOCUMENT_ASSETS
  │
  ├── STUDY_SESSIONS
  │       │
  │       └── STUDY_PROGRESS
  │
  └── QUIZ_SESSIONS
          │
          ├── QUIZ_TOPIC_PROGRESS
          │       │
          │       └── QUESTIONS
          │               │
          │               ├── QUESTION_OPTIONS
          │               │
          │               └── USER_ANSWERS
          │                       │
          │                       ├── VOICE_RECORDINGS
          │                       │
          │                       └── ANSWER_EVALUATIONS
          │
          ├── QUIZ_TOPIC_SCORES
          │
          └── QUIZ_RESULTS

PDF_DOCUMENTS
      │
      └── SOURCE_REFERENCES
```

This architecture supports the complete Version 1 application.

---

# 76. Part 4 Completion Status

TAD Part 4 defines:

- PostgreSQL architecture
- pgvector architecture
- User database
- User profile database
- PDF database
- PDF processing jobs
- PDF pages
- PDF sections
- Topic structure
- Content chunks
- Chunk embeddings
- Document assets
- Study sessions
- Study progress
- Quiz sessions
- Quiz topic progression
- Questions
- Question options
- User answers
- Answer evaluations
- Voice recordings
- Topic scores
- Final quiz results
- Source references
- Complete entity relationships
- Foreign keys
- Delete rules
- Database indexes
- pgvector indexing
- RAG isolation
- Quiz scoring
- Study time tracking
- PDF usage tracking
- Dashboard data flow
- Data ownership
- Security rules
- Data lifecycle
- Migration order
- Laravel model structure
- AI and database separation

---

# 77. Next Part

The next document is:

# TAD Part 5 - Backend and API Architecture

Part 5 will define:

1. Laravel backend architecture

2. Python FastAPI architecture

3. ReactJS to Laravel communication

4. Laravel to Python communication

5. Authentication flow

6. PDF upload API flow

7. PDF processing API flow

8. Study Mode APIs

9. Quiz Mode APIs

10. Question APIs

11. Answer submission APIs

12. Voice answer APIs

13. Progress APIs

14. Dashboard APIs

15. Error handling

16. Validation

17. Authentication and authorization

18. Queue architecture

19. Redis usage

20. Background jobs

21. API request and response flow

22. AI API key management

23. Gemini API integration boundary

24. Complete backend request lifecycle

---

# End of TAD Part 4
