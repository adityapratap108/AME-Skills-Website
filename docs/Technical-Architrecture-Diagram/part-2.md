# 02_Technical_Architecture_Document.md

# Part 2 - Detailed Component Architecture and Request Flows

**Project Name:** AME AI Tutor

**Document ID:** TAD-01-Part-2

**Version:** 1.0

**Status:** Draft

**Depends On:** TAD-01-Part-1

---

# 1. Purpose

This document defines the detailed technical architecture and request flows of the AME AI Tutor application.

Part 1 defined the high-level system architecture.

This part defines how the major components of the application communicate with each other.

The main components covered in this document are:

- ReactJS Frontend
- Laravel Backend
- Python AI Service
- PostgreSQL Database
- pgvector
- Cloudflare R2 Storage
- Gemini API
- Laravel Queue System

This document also defines the following workflows:

- User Authentication Flow
- PDF Upload Flow
- PDF Processing Flow
- PDF Type Detection Flow
- Study Mode Flow
- Quiz Mode Flow
- Topic-by-Topic Quiz Flow
- Text Answer Evaluation Flow
- Voice Answer Evaluation Flow
- Image Display Flow
- Source Reference Flow
- Study Time Tracking Flow
- Quiz Progress Tracking Flow
- Final Score Calculation Flow
- Error Handling Flow

---

# 2. Overall System Architecture

The AME AI Tutor application will use a multi-service architecture.

The main architecture is:

```text
                         ┌───────────────────┐
                         │       USER        │
                         │                   │
                         │     Student       │
                         └─────────┬─────────┘
                                   │
                                   │ HTTPS
                                   ▼
                    ┌─────────────────────────────┐
                    │       REACTJS FRONTEND      │
                    │                             │
                    │  Authentication             │
                    │  Dashboard                  │
                    │  PDF Upload                 │
                    │  PDF Library                │
                    │  Study Mode                 │
                    │  Quiz Mode                  │
                    │  Voice Answers              │
                    │  Progress                   │
                    │  Results                    │
                    └──────────────┬──────────────┘
                                   │
                                   │ REST API
                                   ▼
                    ┌─────────────────────────────┐
                    │       LARAVEL BACKEND       │
                    │                             │
                    │  Authentication             │
                    │  Authorization              │
                    │  User Management            │
                    │  PDF Management              │
                    │  Study Management            │
                    │  Quiz Management             │
                    │  Progress Management         │
                    │  Queue Management            │
                    │  AI Service Communication    │
                    └───────┬───────────┬─────────┘
                            │           │
                 ┌──────────┘           └──────────┐
                 │                                 │
                 ▼                                 ▼
       ┌──────────────────────┐         ┌──────────────────────┐
       │     PostgreSQL       │         │    Cloudflare R2     │
       │                      │         │                      │
       │ Users                │         │ Original PDFs        │
       │ PDFs                 │         │ Extracted Images     │
       │ Topics               │         │ Voice Recordings     │
       │ Chunks               │         │ Other Files          │
       │ Study Sessions       │         │                      │
       │ Quiz Sessions        │         │                      │
       │ Questions            │         │                      │
       │ Answers              │         │                      │
       │ Scores               │         │                      │
       │ Progress             │         │                      │
       │                      │         │                      │
       │ pgvector             │         │                      │
       └──────────┬───────────┘         └──────────┬───────────┘
                  │                                │
                  └────────────────┬───────────────┘
                                   │
                                   ▼
                    ┌─────────────────────────────┐
                    │     PYTHON AI SERVICE       │
                    │                             │
                    │  PDF Processing             │
                    │  PDF Type Detection         │
                    │  OCR                        │
                    │  Text Extraction            │
                    │  Image Extraction           │
                    │  Table Processing            │
                    │  Topic Detection             │
                    │  Chunking                   │
                    │  Embeddings                 │
                    │  Vector Search              │
                    │  RAG                        │
                    │  Quiz Generation             │
                    │  Answer Evaluation           │
                    │  Voice Processing            │
                    └──────────────┬──────────────┘
                                   │
                                   │ API
                                   ▼
                    ┌─────────────────────────────┐
                    │         GEMINI API           │
                    │                             │
                    │  Context Generation         │
                    │  Explanations               │
                    │  Quiz Generation             │
                    │  Text Evaluation             │
                    │  Voice Answer Evaluation     │
                    └─────────────────────────────┘
```

---

# 3. Main Component Responsibilities

## 3.1 ReactJS Frontend

ReactJS is responsible for:

- User interface
- User interaction
- Authentication screens
- Dashboard
- PDF upload interface
- PDF library
- Study Mode interface
- Quiz Mode interface
- Question interface
- Voice recording interface
- Progress display
- Score display
- Result display

ReactJS must not contain:

- Gemini API keys
- Database credentials
- R2 secret credentials
- Internal Python AI Service credentials

All sensitive operations must go through Laravel.

---

## 3.2 Laravel Backend

Laravel is the main application backend.

Laravel is responsible for:

- Authentication
- Authorization
- User management
- PDF ownership
- PDF upload handling
- PDF metadata
- Study session management
- Quiz session management
- Question management
- Answer management
- Score management
- Progress tracking
- File storage communication
- Python AI Service communication
- Queue management
- API security

---

## 3.3 Python AI Service

The Python AI Service is responsible for AI and document processing.

It handles:

- PDF parsing
- PDF type detection
- OCR
- Text extraction
- Table extraction
- Image extraction
- Document structure detection
- Topic detection
- Chunking
- Embedding generation
- Vector retrieval
- RAG
- AI context construction
- Study explanations
- Quiz generation
- Text answer evaluation
- Voice answer processing

---

## 3.4 PostgreSQL

PostgreSQL stores structured application data.

Examples:

- Users
- PDFs
- Topics
- Chunks
- Study sessions
- Quiz sessions
- Questions
- Answers
- Scores
- Progress
- Source references
- Image metadata

PostgreSQL will also use pgvector for vector similarity search.

---

## 3.5 Cloudflare R2

Cloudflare R2 stores files.

Examples:

- Original PDF files
- Extracted images
- Extracted figures
- Voice recordings

Large binary files should not be stored directly inside PostgreSQL.

---

## 3.6 Gemini API

Gemini will be used as the external AI model.

It may be used for:

- PDF context generation
- Study explanations
- Quiz question generation
- Text answer evaluation
- Voice answer evaluation

The Gemini API must be called through the Python AI Service.

The ReactJS frontend must never directly expose the Gemini API key.

---

# 4. ReactJS Frontend Architecture

The ReactJS application should be organized into feature-based modules.

Recommended structure:

```text
src/
│
├── api/
│
├── components/
│
├── layouts/
│
├── pages/
│
├── features/
│   ├── auth/
│   ├── dashboard/
│   ├── pdf/
│   ├── study/
│   ├── quiz/
│   ├── voice/
│   └── progress/
│
├── hooks/
│
├── context/
│
├── services/
│
├── routes/
│
├── utils/
│
├── types/
│
└── App.jsx
```

---

# 5. ReactJS Main Modules

## 5.1 Authentication

Responsibilities:

- Login
- Registration
- Logout
- Authentication state
- Protected routes
- Session handling

The frontend communicates with Laravel for authentication.

## 5.2 Dashboard

The dashboard displays:

- User name
- User details
- Uploaded PDFs
- Upload date
- Processing status
- Study time
- Testing time
- Study progress
- Quiz scores
- Overall progress

## 5.3 PDF Upload

The PDF upload module handles:

- PDF selection
- File validation
- File size validation
- Upload progress
- Upload status
- Processing status
- Processing errors

## 5.4 PDF Library

The PDF library displays:

- PDF name
- Upload date
- Processing status
- Study time
- Testing time
- Progress
- Last activity

## 5.5 Study Mode

Study Mode displays:

- PDF context
- Current topic
- Topic explanation
- Key points
- Source references
- Relevant images
- Current progress

## 5.6 Quiz Mode

Quiz Mode displays:

- PDF context
- Current topic
- Questions
- Answer options
- Text answer fields
- Voice recording interface
- Skip option
- Submit button
- Topic score
- Final score

## 5.7 Voice Module

The voice module handles:

- Microphone permission
- Recording
- Stop recording
- Recording preview
- Submission
- Processing status

---

# 6. Laravel Backend Architecture

Laravel should follow a layered architecture.

Recommended structure:

```text
app/
│
├── Http/
│   ├── Controllers/
│   ├── Requests/
│   └── Resources/
│
├── Models/
│
├── Services/
│
├── Jobs/
│
├── Events/
│
├── Listeners/
│
├── Policies/
│
└── Exceptions/
```

---

# 7. Laravel Controllers

Main controllers:

```text
AuthController

UserController

DashboardController

PdfController

StudyController

QuizController

QuestionController

AnswerController

ProgressController

VoiceController
```

Controllers should remain thin.

Controllers should:

1. Receive request.
2. Validate request.
3. Call the appropriate service.
4. Return API response.

Business logic should be placed in Services.

---

# 8. Laravel Services

Recommended services:

```text
AuthService

UserService

PdfService

PdfProcessingService

StudyService

QuizService

QuestionService

AnswerService

ProgressService

DashboardService

AiService

StorageService
```

Each service should handle its own business responsibility.

---

# 9. Laravel AI Service Client

Laravel will communicate with Python through an internal API client.

The flow is:

```text
ReactJS
   │
   ▼
Laravel Controller
   │
   ▼
Laravel Service
   │
   ▼
AI Service Client
   │
   ▼
Python AI Service
```

The AI Service Client will handle:

- PDF processing requests
- PDF context requests
- Study explanation requests
- Quiz generation requests
- Text evaluation requests
- Voice evaluation requests

---

# 10. Laravel Queue Architecture

Long-running tasks must not block normal HTTP requests.

PDF processing should use Laravel Queue.

Flow:

```text
User Uploads PDF
       │
       ▼
Laravel API
       │
       ▼
Create PDF Record
       │
       ▼
Store PDF
       │
       ▼
Dispatch ProcessPdfJob
       │
       ▼
Queue
       │
       ▼
Queue Worker
       │
       ▼
Python AI Service
```

The frontend can immediately receive:

```text
PDF uploaded successfully.
Processing has started.
```

The user can later check the processing status.

---

# 11. PDF Processing Status

The PDF record should maintain a processing status.

Recommended statuses:

```text
UPLOADING

UPLOADED

PROCESSING

READY

FAILED
```

Flow:

```text
UPLOADING
    │
    ▼
UPLOADED
    │
    ▼
PROCESSING
    │
    ├── Success ──> READY
    │
    └── Failure ──> FAILED
```

---

# 12. Python AI Service Architecture

The Python AI Service should be organized into modules.

Recommended structure:

```text
ai-service/
│
├── app/
│   ├── api/
│   ├── core/
│   ├── pdf/
│   ├── ocr/
│   ├── extraction/
│   ├── topics/
│   ├── chunking/
│   ├── embeddings/
│   ├── retrieval/
│   ├── rag/
│   ├── study/
│   ├── quiz/
│   ├── evaluation/
│   ├── voice/
│   └── gemini/
│
├── tests/
│
├── requirements.txt
│
└── main.py
```

The Python AI Service should use FastAPI for internal APIs.

---

# 13. Python AI Service Responsibilities

The Python AI Service handles:

```text
PDF
 │
 ├── Type Detection
 │
 ├── Text Extraction
 │
 ├── OCR
 │
 ├── Image Extraction
 │
 ├── Table Extraction
 │
 ├── Structure Detection
 │
 ├── Topic Detection
 │
 ├── Chunking
 │
 ├── Embeddings
 │
 ├── Vector Search
 │
 └── RAG
```

It also handles:

```text
Study Generation

Quiz Generation

Text Evaluation

Voice Processing

Voice Answer Evaluation
```

---

# 14. Python Internal API Endpoints

Recommended endpoints:

```text
POST /internal/pdf/process

POST /internal/study/context

POST /internal/study/explain

POST /internal/quiz/generate

POST /internal/quiz/evaluate

POST /internal/voice/evaluate
```

These APIs should be private.

They should not be exposed directly to public users.

Only Laravel should be allowed to call these endpoints.

---

# 15. PDF Upload Flow

The complete PDF upload flow is:

```text
Student
   │
   ▼
ReactJS
   │
   │ PDF Upload
   ▼
Laravel
   │
   ├── Authenticate User
   │
   ├── Validate File
   │
   ├── Validate Size
   │
   └── Validate Extension
   │
   ▼
Cloudflare R2
   │
   ▼
Create PDF Database Record
   │
   ▼
Status = UPLOADED
   │
   ▼
Dispatch ProcessPdfJob
   │
   ▼
Queue Worker
   │
   ▼
Python AI Service
```

The user does not have to wait for complete AI processing during the upload request.

---

# 16. PDF Type Detection

The system should automatically identify:

- Digital PDF
- Scanned PDF
- Mixed PDF

The detection process is:

```text
PDF
 │
 ▼
Analyze Each Page
 │
 ▼
Attempt Text Extraction
 │
 ├── Good Text
 │      │
 │      ▼
 │   Digital Page
 │
 ├── No Text
 │      │
 │      ▼
 │   Scanned Page
 │
 └── Partial Text
        │
        ▼
    Mixed Page
```

The complete PDF classification can then be:

```text
All Digital Pages
       │
       ▼
Digital PDF


All Scanned Pages
       │
       ▼
Scanned PDF


Combination
       │
       ▼
Mixed PDF
```

---

# 17. Digital PDF Processing

For digital PDFs:

1. Extract text.
2. Preserve page number.
3. Detect headings.
4. Detect sections.
5. Detect paragraphs.
6. Extract tables.
7. Extract images.
8. Store source metadata.

Example metadata:

```json
{
  "document_id": 123,
  "page_number": 42,
  "section": "Hydraulic System",
  "heading": "Hydraulic Pumps",
  "content_type": "text"
}
```

---

# 18. Scanned PDF Processing

For scanned pages:

1. Render page as image.
2. Perform OCR.
3. Extract text.
4. Store OCR output.
5. Preserve page number.
6. Store OCR confidence where available.

Flow:

```text
Scanned Page
     │
     ▼
Page Image
     │
     ▼
OCR
     │
     ▼
Extracted Text
     │
     ▼
Page Metadata
```

---

# 19. Mixed PDF Processing

A mixed PDF can contain both digital and scanned pages.

Example:

```text
Page 1 → Digital

Page 2 → Digital

Page 3 → Scanned

Page 4 → Digital

Page 5 → Scanned
```

Only scanned pages should require OCR.

This reduces unnecessary processing.

---

# 20. PDF Page Rotation Handling

The system should detect page rotation information.

Possible rotations:

```text
0°

90°

180°

270°
```

Before OCR or visual processing, pages should be normalized when required.

Example:

```text
Original Page
     │
     ▼
Detect Rotation
     │
     ▼
Normalize Orientation
     │
     ▼
OCR / Processing
```

The original page number must remain unchanged.

This is important because source references must still point to the original PDF page.

---

# 21. Table Processing

Tables should be treated as structured content.

Example:

```text
Table
 │
 ├── Header Row
 │
 ├── Row 1
 │
 ├── Row 2
 │
 └── Row 3
```

The system should preserve table relationships where possible.

Tables should be associated with:

- Document ID
- Page number
- Section
- Topic

This allows the RAG system to retrieve table information when needed.

---

# 22. Image and Figure Processing

Images and figures should be extracted separately.

Flow:

```text
PDF Page
   │
   ▼
Image / Figure Detection
   │
   ▼
Extract Image
   │
   ▼
Save Image to R2
   │
   ▼
Create Image Metadata
   │
   ▼
Associate With Page
   │
   ▼
Associate With Topic / Chunk
```

Example:

```json
{
  "image_id": "img_42_01",
  "document_id": 123,
  "page_number": 42,
  "caption": "Hydraulic System Diagram",
  "storage_key": "documents/123/images/img_42_01.png"
}
```

---

# 23. Figure Reference Flow

If a student asks:

```text
Explain Figure 4.2
```

The system should:

```text
User Question
      │
      ▼
Identify Figure Reference
      │
      ▼
Search PDF Metadata
      │
      ▼
Find Figure 4.2
      │
      ├── Figure Image
      ├── Figure Caption
      ├── Page Number
      └── Related Text
      │
      ▼
Build RAG Context
      │
      ▼
Gemini
      │
      ▼
Generate Explanation
      │
      ▼
Return:
      ├── Explanation
      ├── Source
      └── Figure Image
```

The frontend can then display:

```text
Figure 4.2

[Figure Image]

Explanation:
...

Source:
Page 42
Section: Hydraulic System
```

---

# 24. Content Metadata

Every extracted content unit should preserve metadata.

Recommended metadata:

```text
Document ID

Page Number

Section

Heading

Topic ID

Content Type

Source Reference
```

Content types may include:

```text
TEXT

TABLE

IMAGE

FIGURE

OCR_TEXT
```

---

# 25. Document Structure Detection

After extraction, the system should attempt to identify:

```text
Document
 │
 ├── Chapter
 │
 ├── Section
 │
 ├── Subsection
 │
 ├── Topic
 │
 └── Content
```

This structure will be used by:

- Study Mode
- Quiz Mode
- RAG
- Source References
- Progress Tracking

---

# 26. Topic Detection

The AI pipeline should identify topics from the document.

Example:

```text
AME PDF
 │
 ├── Aircraft Structures
 │
 ├── Materials
 │
 ├── Hydraulic Systems
 │
 ├── Landing Gear
 │
 └── Flight Controls
```

Each topic should be stored with:

- Topic ID
- Document ID
- Topic name
- Topic order
- Source sections

---

# 27. Chunking Pipeline

The extracted document will be divided into chunks.

The preferred process is:

```text
Extracted Content
       │
       ▼
Document Structure
       │
       ▼
Sections
       │
       ▼
Topics
       │
       ▼
Semantic Chunking
       │
       ▼
Chunk Metadata
```

Chunking should prefer semantic boundaries.

The system should avoid splitting content in the middle of an important concept whenever possible.

---

# 28. Chunk Metadata

Each chunk should contain:

```text
Chunk ID

Document ID

Topic ID

Page Number

Section

Heading

Content Type

Chunk Text
```

Example:

```json
{
  "chunk_id": "chunk_00042",
  "document_id": 123,
  "topic_id": 15,
  "page_number": 42,
  "section": "Hydraulic System",
  "heading": "Hydraulic Pumps",
  "content_type": "text",
  "content": "Hydraulic pumps are used..."
}
```

---

# 29. Embedding Pipeline

After chunking:

```text
Chunk Text
     │
     ▼
Embedding Model
     │
     ▼
Vector
     │
     ▼
PostgreSQL + pgvector
```

The vector is stored along with the chunk metadata.

This allows semantic search.

---

# 30. RAG Retrieval Pipeline

The RAG pipeline is:

```text
User Query
     │
     ▼
Query Embedding
     │
     ▼
pgvector Search
     │
     ▼
Retrieve Relevant Chunks
     │
     ▼
Filter by Document ID
     │
     ▼
Filter by Topic ID if required
     │
     ▼
Build Context
     │
     ▼
Send Context to Gemini
     │
     ▼
Generate Response
```

The retrieval process must be restricted to the correct PDF.

This is important for preventing information from one user's PDF from being mixed with another PDF.

---

# 31. Study Mode Flow

When the user selects:

```text
Study from PDF
```

The flow is:

```text
Student
    │
    ▼
ReactJS
    │
    ▼
Laravel
    │
    ├── Verify User
    ├── Verify PDF Ownership
    └── Create Study Session
    │
    ▼
Python AI Service
    │
    ├── Get Current Topic
    ├── Retrieve Topic Chunks
    ├── Build RAG Context
    └── Request Explanation
    │
    ▼
Gemini
    │
    ▼
AI Explanation
    │
    ├── Explanation
    ├── Key Points
    ├── Sources
    └── Images
    │
    ▼
Laravel
    │
    ▼
ReactJS
    │
    ▼
Student
```

---

# 32. Study Mode Response

The AI Service should return structured data.

Example:

```json
{
  "topic": "Hydraulic Pumps",
  "explanation": "Hydraulic pumps are used...",
  "key_points": ["Point 1", "Point 2", "Point 3"],
  "sources": [
    {
      "page": 42,
      "section": "Hydraulic System"
    }
  ],
  "images": [
    {
      "image_id": "img_42_01",
      "page": 42
    }
  ]
}
```

ReactJS can use this response to render the Study Mode screen.

---

# 33. Quiz Mode Flow

When the user selects:

```text
Test My Skills
```

The flow is:

```text
Student
    │
    ▼
ReactJS
    │
    ▼
Laravel
    │
    ├── Verify User
    ├── Verify PDF Ownership
    └── Create Quiz Session
    │
    ▼
Python AI Service
    │
    ├── Select Current Topic
    ├── Retrieve Topic Chunks
    ├── Build RAG Context
    └── Generate Questions
    │
    ▼
Gemini
    │
    ▼
Questions
    │
    ▼
Laravel
    │
    ▼
ReactJS
    │
    ▼
Student
```

---

# 34. Topic-by-Topic Quiz Flow

The quiz system will not generate all questions for the entire PDF at once.

The system will work topic-by-topic.

Example:

```text
PDF
 │
 ▼
Topic 1
 │
 ▼
Generate Questions
 │
 ▼
User Attempts
 │
 ▼
Submit
 │
 ▼
Evaluate
 │
 ▼
Save Topic Score
 │
 ▼
Topic 2
 │
 ▼
Generate Questions
 │
 ▼
User Attempts
 │
 ▼
Submit
 │
 ▼
Evaluate
 │
 ▼
Save Topic Score
 │
 ▼
Continue
 │
 ▼
All Topics Completed
 │
 ▼
Calculate Final Score
```

---

# 35. Question Generation

Questions should be generated only from the current topic.

The AI Service should provide Gemini with:

- Current topic
- Relevant chunks
- PDF information
- Question types
- Difficulty requirements
- Question count
- Quiz rules

Questions should be grounded in the uploaded PDF.

---

# 36. Supported Question Types

The application supports:

```text
Single Choice

Multiple Choice

Text Based

Voice Based
```

Each question must contain a question type.

---

# 37. Question Difficulty

The quiz system should follow the approved difficulty distribution.

For 10 questions:

```text
Easy   = 3

Medium = 4

Hard   = 3
```

Easy and Medium questions should generally be more common than Hard questions.

---

# 38. Minimum Text and Voice Questions

Every quiz should contain at least:

```text
3 Text-Based Questions

3 Voice-Based Questions
```

There is no fixed ratio between the total number of text and voice questions beyond this minimum requirement.

---

# 39. Answer Submission

The user can modify an answer until the Submit button is clicked.

Flow:

```text
Question
   │
   ▼
User Selects / Enters Answer
   │
   ▼
User Changes Answer
   │
   ▼
User Changes Answer Again
   │
   ▼
User Clicks Submit
   │
   ▼
Answer Locked
```

After submission, the answer cannot be modified for that attempt.

---

# 40. Skipped Questions

If a user skips a question:

```text
Question
   │
   ▼
Skip
   │
   ▼
Unattempted
   │
   ▼
Score = 0
```

Skipped questions do not receive marks.

There is no negative marking.

---

# 41. No Negative Marking

The scoring system is:

```text
Correct Answer
    │
    ▼
Marks Awarded

Incorrect Answer
    │
    ▼
0 Marks

Skipped Answer
    │
    ▼
0 Marks
```

No negative marks are deducted.

---

# 42. Passing Score

The passing score is:

```text
75%
```

Result rules:

```text
Score >= 75%
    │
    ▼
PASS
```

```text
Score < 75%
    │
    ▼
FAIL
```

---

# 43. Multiple Choice Evaluation

Single-choice and multiple-choice questions should primarily use deterministic evaluation.

The system should store:

```text
Correct Answer

User Answer

Question Marks
```

For multiple-choice questions, the evaluation rules should be explicitly defined by the backend.

The AI should not be responsible for deciding whether a multiple-choice answer is correct.

---

# 44. Text Answer Evaluation

Text answers require AI-assisted evaluation.

Flow:

```text
Student Text Answer
       │
       ▼
ReactJS
       │
       ▼
Laravel
       │
       ▼
Python AI Service
       │
       ├── Retrieve Question
       ├── Retrieve Correct Context
       ├── Retrieve Expected Answer
       └── Build Evaluation Context
       │
       ▼
Gemini
       │
       ▼
Evaluate Answer
       │
       ├── Correctness
       ├── Relevance
       └── Score
       │
       ▼
Laravel
       │
       ▼
Save Evaluation
```

The evaluation should be based on the uploaded PDF content.

The AI should not evaluate the answer only using general knowledge.

---

# 45. Voice Question Flow

The voice question flow is:

```text
AI Generates Voice Question
        │
        ▼
Question Displayed
        │
        ▼
Student Reads Question
        │
        ▼
Student Records Answer
        │
        ▼
ReactJS
        │
        ▼
Laravel
        │
        ▼
Python AI Service
        │
        ▼
Speech-to-Text
        │
        ▼
Transcribed Answer
        │
        ▼
Retrieve PDF Context
        │
        ▼
Gemini
        │
        ▼
Evaluate Answer
        │
        ▼
Score
        │
        ▼
Laravel
        │
        ▼
ReactJS
```

---

# 46. Voice Answer Evaluation

The system should evaluate the meaning of the student's answer.

The system should not require the student to use exactly the same wording as the expected answer.

Example:

```text
Expected Answer:
"The hydraulic pump provides hydraulic pressure."

Student Answer:
"The pump creates the pressure needed for the hydraulic system."
```

The AI should be able to recognize that the meaning is substantially similar.

The evaluation must still be grounded in the relevant PDF content.

---

# 47. Voice Recording Storage

If voice recordings are stored:

```text
ReactJS
    │
    ▼
Laravel
    │
    ├── Validate Recording
    ├── Validate File Type
    └── Validate File Size
    │
    ▼
Cloudflare R2
    │
    ▼
Python AI Service
    │
    ├── Retrieve Audio
    ├── Speech-to-Text
    └── Evaluate Answer
    │
    ▼
Laravel
    │
    ▼
Save Result
```

The database should store the R2 object key.

The actual audio file should be stored in R2.

---

# 48. Source Reference Architecture

AI-generated content should maintain source references wherever possible.

Example:

```json
{
  "source": {
    "document_id": 123,
    "page_number": 42,
    "section": "Hydraulic System",
    "heading": "Hydraulic Pumps"
  }
}
```

The frontend can display:

```text
Source

PDF: Aircraft Hydraulic Systems

Page: 42

Section: Hydraulic System

Topic: Hydraulic Pumps
```

This allows the student to understand where the AI response came from.

---

# 49. Image Display Flow

When an AI response references an image:

```text
AI Response
    │
    ▼
Image ID
    │
    ▼
Laravel
    │
    ▼
Verify User Access
    │
    ▼
Generate Authorized Image URL
    │
    ▼
ReactJS
    │
    ▼
Display Image
```

Images should not be publicly accessible without authorization.

The frontend should not receive R2 secret credentials.

---

# 50. PDF Context Generation

After PDF processing is complete, the system should generate a high-level context.

The context may include:

- PDF title
- Subject
- Main topics
- Sections
- General scope
- Summary

Flow:

```text
Processed PDF
      │
      ▼
Topics + Sections
      │
      ▼
Relevant Content
      │
      ▼
Gemini
      │
      ▼
PDF Context
      │
      ▼
PostgreSQL
```

This context is shown before Study Mode or Quiz Mode begins.

---

# 51. Study Session Tracking

When Study Mode starts:

```text
Start Study
     │
     ▼
Create Study Session
     │
     ▼
Start Time
     │
     ▼
User Studies
     │
     ▼
Track Active Time
     │
     ▼
End Session
     │
     ▼
Calculate Duration
     │
     ▼
Save Study Time
```

The study session should be associated with:

- User ID
- PDF ID
- Topic ID
- Start time
- End time
- Duration

---

# 52. Quiz Session Tracking

When Quiz Mode starts:

```text
Start Quiz
     │
     ▼
Create Quiz Session
     │
     ▼
Select Topic
     │
     ▼
Generate Questions
     │
     ▼
User Attempts
     │
     ▼
Submit
     │
     ▼
Evaluate
     │
     ▼
Save Topic Score
     │
     ▼
Next Topic
     │
     ▼
All Topics Complete
     │
     ▼
Calculate Final Score
```

The quiz session should be associated with:

- User ID
- PDF ID
- Topic IDs
- Question IDs
- Answer IDs
- Scores
- Start time
- End time

---

# 53. Final Score Calculation

After all required topics are completed, the system calculates the final score.

Example:

```text
Topic 1 = 80%

Topic 2 = 70%

Topic 3 = 90%

Topic 4 = 75%
```

The final score should follow the scoring rules defined in the SRS.

The final result displays:

```text
Final Score: XX%

Status: PASS / FAIL
```

Passing score:

```text
75%
```

---

# 54. General API Request Flow

The standard request flow is:

```text
ReactJS
   │
   │ HTTPS / REST
   ▼
Laravel API
   │
   ├── Authentication
   ├── Authorization
   ├── Validation
   └── Business Logic
   │
   ▼
Database / Storage / AI Service
   │
   ▼
Laravel
   │
   ▼
ReactJS
```

ReactJS must not bypass Laravel for application operations.

---

# 55. General AI Request Flow

The standard AI request flow is:

```text
ReactJS
    │
    ▼
Laravel
    │
    ├── Authenticate User
    ├── Verify PDF Ownership
    ├── Validate Request
    │
    ▼
Python AI Service
    │
    ├── Retrieve PDF Content
    ├── Retrieve Relevant Chunks
    ├── Build RAG Context
    │
    ▼
Gemini
    │
    ▼
AI Result
    │
    ▼
Python AI Service
    │
    ▼
Laravel
    │
    ▼
ReactJS
```

This architecture ensures that AI requests are authenticated and associated with an authorized document.

---

# 56. AI Failure Handling

If Gemini fails:

```text
Python AI Service
       │
       ▼
Gemini API
       │
       X
     Error
       │
       ▼
Retry / Controlled Failure
       │
       ▼
Laravel
       │
       ▼
ReactJS
```

The application should not expose raw internal API errors.

The user should receive a simple message such as:

```text
We could not process your request right now.
Please try again.
```

---

# 57. PDF Processing Failure

If PDF processing fails:

```text
PDF
 │
 ▼
Processing
 │
 X
Failure
 │
 ▼
Status = FAILED
 │
 ▼
Store Error Information
 │
 ▼
Notify Frontend
```

A PDF should be marked as READY only after required processing has completed successfully.

---

# 58. User Data Ownership

Every user-owned resource must be associated with a user.

Example:

```text
User
 │
 ├── PDFs
 │
 ├── Study Sessions
 │
 ├── Quiz Sessions
 │
 ├── Questions
 │
 ├── Answers
 │
 └── Progress
```

Laravel authorization policies must verify ownership before allowing access.

---

# 59. PDF Access Control

When a user requests a PDF:

```text
Request PDF
     │
     ▼
Laravel
     │
     ▼
Check Authentication
     │
     ▼
Check PDF Ownership
     │
     ├── Authorized
     │      │
     │      ▼
     │   Continue
     │
     └── Unauthorized
            │
            ▼
          Reject
```

A user must never be able to access another user's PDF.

---

# 60. AI Context Isolation

RAG retrieval must be restricted to the correct document.

Example:

```text
User A
  │
  ▼
PDF A
  │
  ▼
RAG Search
  │
  ▼
Only PDF A Chunks
```

The system must not accidentally retrieve content from:

```text
User B

PDF B
```

This is a critical security requirement.

---

# 61. Complete PDF Lifecycle

The complete PDF lifecycle is:

```text
1. User Selects PDF
        │
        ▼
2. ReactJS Uploads PDF
        │
        ▼
3. Laravel Validates PDF
        │
        ▼
4. Laravel Creates PDF Record
        │
        ▼
5. PDF Stored in R2
        │
        ▼
6. Status = UPLOADED
        │
        ▼
7. ProcessPdfJob Created
        │
        ▼
8. Queue Worker Starts
        │
        ▼
9. Python AI Service Processes PDF
        │
        ▼
10. Detect PDF Type
        │
        ▼
11. Extract Text / OCR
        │
        ▼
12. Extract Images
        │
        ▼
13. Extract Tables
        │
        ▼
14. Detect Document Structure
        │
        ▼
15. Detect Topics
        │
        ▼
16. Create Chunks
        │
        ▼
17. Generate Embeddings
        │
        ▼
18. Store Vectors
        │
        ▼
19. Generate PDF Context
        │
        ▼
20. Save Processing Result
        │
        ▼
21. Status = READY
        │
        ▼
22. User Can Study or Test
```

---

# 62. Complete Study Mode Lifecycle

The complete Study Mode lifecycle is:

```text
1. User Opens PDF
        │
        ▼
2. User Selects Study Mode
        │
        ▼
3. Laravel Creates Study Session
        │
        ▼
4. PDF Context Displayed
        │
        ▼
5. Current Topic Selected
        │
        ▼
6. Relevant Chunks Retrieved
        │
        ▼
7. RAG Context Built
        │
        ▼
8. Gemini Generates Explanation
        │
        ▼
9. Explanation Displayed
        │
        ├── Topic
        ├── Explanation
        ├── Key Points
        ├── Sources
        └── Images
        │
        ▼
10. User Continues
        │
        ▼
11. Next Topic
        │
        ▼
12. Continue Until Required Content Is Covered
        │
        ▼
13. Study Session Ends
        │
        ▼
14. Study Time Saved
```

---

# 63. Complete Quiz Mode Lifecycle

The complete Quiz Mode lifecycle is:

```text
1. User Opens PDF
        │
        ▼
2. User Selects Test My Skills
        │
        ▼
3. Laravel Creates Quiz Session
        │
        ▼
4. PDF Context Displayed
        │
        ▼
5. Select Current Topic
        │
        ▼
6. Retrieve Topic Content
        │
        ▼
7. Generate Questions
        │
        ▼
8. User Attempts Questions
        │
        ▼
9. User Submits
        │
        ▼
10. Evaluate Answers
        │
        ▼
11. Save Topic Score
        │
        ▼
12. Move to Next Topic
        │
        ▼
13. Repeat
        │
        ▼
14. All Topics Completed
        │
        ▼
15. Calculate Final Score
        │
        ▼
16. Check Passing Score
        │
        ▼
17. Display Final Result
```

---

# 64. Locked Architecture Decisions

The following architecture decisions are considered locked for Version 1:

1. ReactJS will be used for the frontend.

2. Laravel will be used as the main backend.

3. Python will be used as the AI and document-processing service.

4. FastAPI will be used for the Python AI Service API.

5. ReactJS will communicate with Laravel.

6. Laravel will communicate with Python AI Service.

7. ReactJS will not directly communicate with Gemini.

8. Gemini API keys will remain server-side.

9. PDF processing will be asynchronous.

10. Laravel Queue will be used for long-running PDF processing.

11. Cloudflare R2 will store original PDFs.

12. Cloudflare R2 will store extracted images and figures.

13. Cloudflare R2 may store voice recordings.

14. PostgreSQL will store application data.

15. pgvector will be used for vector search.

16. Digital PDFs will use text extraction.

17. Scanned PDFs will use OCR.

18. Mixed PDFs will use both text extraction and OCR as required.

19. PDF pages will retain their original page numbers.

20. Images and figures will maintain page references.

21. Tables will be processed as structured document content where possible.

22. Document content will be divided into semantic chunks.

23. Chunks will contain source metadata.

24. Embeddings will be generated for searchable chunks.

25. RAG retrieval will be restricted to the correct PDF.

26. Study Mode will work topic-by-topic.

27. Quiz Mode will work topic-by-topic.

28. Quiz questions will be grounded in the uploaded PDF.

29. Single-choice questions will use deterministic evaluation.

30. Multiple-choice questions will use deterministic evaluation.

31. Text answers will use AI-assisted evaluation.

32. Voice answers will use speech-to-text followed by AI-assisted evaluation.

33. Skipped questions will receive zero marks.

34. There will be no negative marking.

35. Passing score will be 75%.

36. Users can modify answers until submission.

37. Submitted answers will be locked.

38. Each quiz will contain at least 3 text-based questions.

39. Each quiz will contain at least 3 voice-based questions.

40. Easy and Medium questions will generally be more common than Hard questions.

41. Source references will be displayed where available.

42. Relevant PDF figures and images can be displayed in Study Mode.

43. User resources will be protected by ownership checks.

44. RAG context will be isolated between users and documents.

---

# 65. Part 2 Completion Status

Technical Architecture Document Part 2 is complete.

This part defined:

- ReactJS architecture
- Laravel architecture
- Python AI Service architecture
- FastAPI internal API
- PostgreSQL responsibility
- pgvector responsibility
- Cloudflare R2 responsibility
- Gemini responsibility
- Laravel Queue architecture
- PDF upload flow
- PDF processing flow
- PDF type detection
- Digital PDF processing
- Scanned PDF processing
- Mixed PDF processing
- Page rotation handling
- OCR flow
- Table processing
- Image extraction
- Figure processing
- Figure reference flow
- Document structure detection
- Topic detection
- Chunking
- Embeddings
- RAG retrieval
- Study Mode
- Quiz Mode
- Topic-by-topic quiz flow
- Question generation
- Question difficulty
- Text answer evaluation
- Voice answer evaluation
- Source references
- Image display
- Study session tracking
- Quiz session tracking
- Score calculation
- Error handling
- Security and ownership
- AI context isolation

---

# 66. Next Part

The next document should be:

# TAD Part 3 - Exact PDF Processing and RAG Pipeline

Part 3 will define the technical implementation of the AI pipeline in detail.

It will cover:

1. Exact PDF processing libraries

2. Exact OCR tools

3. Digital PDF parsing

4. Scanned PDF processing

5. Mixed PDF processing

6. Rotated page handling

7. Table extraction

8. Image extraction

9. Figure detection

10. PDF source mapping

11. Document structure detection

12. Topic detection

13. Semantic chunking

14. Embedding generation

15. pgvector implementation

16. Vector search

17. Metadata filtering

18. RAG pipeline

19. Context construction

20. Gemini integration

21. Hallucination control

22. Source citation generation

23. Image and figure retrieval

24. Study Mode RAG flow

25. Quiz Mode RAG flow

26. Text answer evaluation pipeline

27. Voice answer evaluation pipeline

28. Exact AI Service workflow

29. Failure handling

30. Processing status management

---

# End of TAD Part 2
