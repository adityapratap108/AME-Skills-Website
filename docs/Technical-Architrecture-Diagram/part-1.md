# 02_Technical_Architecture_Document.md

## Part 1 - System Architecture and Technology Stack

**Project Name:** AME AI Tutor

**Document ID:** TAD-01-Part-1

**Version:** 1.0

**Status:** Draft

---

# 1. Purpose

This document defines the technical architecture of the AME AI Tutor application.

The application is designed specifically for students studying Aircraft Maintenance Engineering (AME).

The system will allow students to:

- Upload AME-related PDF documents.
- Process and understand the uploaded PDFs.
- Study the PDF content using AI.
- Test their knowledge using AI-generated quizzes.
- Answer questions using different formats.
- Track their study progress.
- Track their quiz progress and scores.

The system will use a combination of:

- ReactJS
- Laravel / PHP
- Python
- PostgreSQL
- pgvector
- Cloudflare R2
- Gemini API

---

# 2. Locked High-Level Architecture

The primary system architecture is:

ReactJS Frontend

↓

Laravel Backend API

↓

Python AI Service

↓

AI / RAG Processing

↓

Gemini API

PostgreSQL

- pgvector
- Cloudflare R2

The architecture can be represented as:

                         ┌──────────────────────┐
                         │       Student        │
                         └──────────┬───────────┘
                                    │
                                    ▼
                         ┌──────────────────────┐
                         │    ReactJS Frontend  │
                         │                      │
                         │  Dashboard           │
                         │  PDF Upload          │
                         │  Study Mode          │
                         │  Quiz Mode            │
                         │  Progress             │
                         │  Results              │
                         └──────────┬───────────┘
                                    │
                              HTTP / HTTPS
                                    │
                                    ▼
                         ┌──────────────────────┐
                         │   Laravel Backend    │
                         │       (PHP)          │
                         │                      │
                         │ Authentication       │
                         │ Authorization        │
                         │ User Management      │
                         │ PDF Management       │
                         │ Study Sessions       │
                         │ Quiz Sessions        │
                         │ Progress             │
                         │ API                  │
                         └───────┬───────┬──────┘
                                 │       │
                         Internal API    │
                                 │       │
                                 ▼       ▼
                    ┌────────────────┐  ┌─────────────────┐
                    │ Python AI      │  │   PostgreSQL    │
                    │ Service        │  │   + pgvector    │
                    │                │  │                 │
                    │ PDF Processing │  │ Users           │
                    │ OCR            │  │ PDFs            │
                    │ Chunking       │  │ Topics          │
                    │ Embeddings     │  │ Study Data      │
                    │ RAG            │  │ Quiz Data       │
                    │ AI Requests    │  │ Embeddings      │
                    └───────┬────────┘  └─────────────────┘
                            │
                            │
                            ▼
                    ┌─────────────────┐
                    │   Gemini API     │
                    │                 │
                    │ AI Generation    │
                    │ AI Evaluation    │
                    └─────────────────┘


                    ┌─────────────────┐
                    │ Cloudflare R2   │
                    │                 │
                    │ Original PDFs   │
                    │ Images          │
                    │ Voice Files     │
                    └─────────────────┘

---

# 3. Technology Stack

The following technologies are locked for Version 1.

| Layer                          | Technology                     |
| ------------------------------ | ------------------------------ |
| Frontend                       | ReactJS                        |
| Frontend Language              | JavaScript / TypeScript        |
| Backend                        | Laravel                        |
| Backend Language               | PHP                            |
| AI Service                     | Python                         |
| AI Framework                   | LangChain                      |
| Database                       | PostgreSQL                     |
| Vector Database                | pgvector                       |
| Object Storage                 | Cloudflare R2                  |
| AI Model Provider              | Gemini API                     |
| Authentication                 | Laravel-based authentication   |
| API Communication              | REST API                       |
| Frontend-Backend Communication | HTTPS                          |
| Backend-AI Communication       | Internal API                   |
| PDF Processing                 | Python PDF Processing Pipeline |
| OCR                            | OCR Pipeline                   |
| Background Processing          | Laravel Queue + Worker         |
| API Documentation              | OpenAPI / Swagger              |
| Version Control                | Git                            |
| Development Environment        | Local Development Environment  |
| Production Environment         | Cloud Deployment               |

---

# 4. Why ReactJS

ReactJS will be used for the frontend application.

ReactJS will handle:

- User Interface
- Dashboard
- PDF Upload
- PDF Listing
- PDF Details
- Study Mode
- Quiz Mode
- Question Interface
- Voice Recording Interface
- Progress Display
- Score Display

ReactJS will not contain:

- Gemini API Keys
- Database Credentials
- R2 Secret Keys
- Python Service Secrets

ReactJS will communicate with Laravel through REST APIs.

---

# 5. Why Laravel

Laravel will be the primary backend application.

Laravel will act as the central business logic layer.

Laravel will handle:

- Authentication
- Authorization
- User Management
- PDF Metadata
- PDF Ownership
- Study Sessions
- Quiz Sessions
- Progress
- Scores
- History
- API Requests
- Request Validation

Laravel will also communicate with:

- PostgreSQL
- Cloudflare R2
- Python AI Service

Laravel will be the only public backend API exposed to the ReactJS frontend.

---

# 6. Why Python

Python will be used for AI and document-processing operations.

Python is suitable for this project because the AI ecosystem has strong support for:

- PDF Processing
- OCR
- Natural Language Processing
- Embeddings
- Vector Search
- RAG
- Gemini Integration
- Speech Processing

The Python service will be responsible for AI-related processing.

It will not replace Laravel as the main application backend.

---

# 7. Why LangChain

LangChain will be used inside the Python AI Service.

LangChain will help manage:

- Document Loading
- Text Splitting
- Embeddings
- Vector Retrieval
- RAG Pipeline
- Prompt Management
- LLM Communication

LangChain will not be responsible for:

- User Authentication
- User Dashboard
- User Management
- Application Business Logic
- PDF Ownership

Those responsibilities remain with Laravel.

---

# 8. Why PostgreSQL

PostgreSQL will be the primary application database.

It will store structured application data.

Examples:

- Users
- User Profiles
- PDFs
- PDF Metadata
- Topics
- Study Sessions
- Quiz Sessions
- Questions
- Answers
- Scores
- Progress

PostgreSQL will also work with pgvector for vector similarity search.

---

# 9. Why pgvector

pgvector is a PostgreSQL extension that allows vector embeddings to be stored and searched inside PostgreSQL.

For Version 1, a separate vector database is not required.

The architecture will use:

PostgreSQL

-

pgvector

This simplifies the architecture.

The system can store:

- Application Data
- PDF Metadata
- Chunk Metadata
- Embeddings

within the same database system.

---

# 10. Why Cloudflare R2

Cloudflare R2 will be used for object storage.

Large files should not be stored directly inside PostgreSQL.

R2 will store:

- Original PDF Files
- Extracted Images
- Voice Recordings, if stored

PostgreSQL will store references and metadata related to these files.

Example:

PostgreSQL

PDF ID
User ID
File Name
File Size
R2 Object Key
Upload Date
Processing Status

The actual PDF file will exist in R2.

---

# 11. Why Gemini API

Gemini will be used as the AI model provider.

The application will use Gemini for tasks such as:

- PDF Context Generation
- Topic Understanding
- Study Explanations
- User Questions
- Quiz Generation
- Text Answer Evaluation
- Voice Answer Evaluation

The Gemini API will only be called from the Python AI Service.

The ReactJS frontend will never directly call Gemini.

---

# 12. Core System Components

The system consists of the following components.

## 12.1 ReactJS Frontend

Responsible for:

- UI
- User Interaction
- API Calls
- Dashboard
- Study Mode
- Quiz Mode

## 12.2 Laravel Backend

Responsible for:

- Authentication
- Authorization
- Business Logic
- Application APIs
- Database Operations
- File Management
- User Data

## 12.3 Python AI Service

Responsible for:

- PDF Processing
- OCR
- Text Extraction
- Image Processing
- Table Processing
- Topic Extraction
- Chunking
- Embeddings
- RAG
- AI Requests
- AI Evaluation

## 12.4 PostgreSQL

Responsible for:

- Structured Data
- User Data
- PDF Metadata
- Study Data
- Quiz Data
- Progress
- Scores

## 12.5 pgvector

Responsible for:

- Vector Embeddings
- Similarity Search
- Semantic Retrieval

## 12.6 Cloudflare R2

Responsible for:

- PDF Storage
- Image Storage
- Voice File Storage

## 12.7 Gemini API

Responsible for:

- AI Generation
- AI Explanation
- AI Evaluation

---

# 13. Responsibility Separation

The architecture follows clear responsibility boundaries.

## ReactJS

Handles:

Presentation
User Interaction
API Communication

## Laravel

Handles:

Authentication
Authorization
Business Logic
Database
Application APIs

## Python

Handles:

AI
PDF Processing
RAG
Embeddings
AI Evaluation

## PostgreSQL

Handles:

Structured Data
Application Records

## pgvector

Handles:

Vector Storage
Semantic Search

## Cloudflare R2

Handles:

Large Binary Files

## Gemini

Handles:

AI Generation
AI Reasoning
AI Evaluation

---

# 14. Main Data Flow

The overall data flow is:

User

↓

ReactJS

↓

Laravel API

↓

Business Logic

↓

PostgreSQL / R2 / Python AI Service

↓

Python AI Pipeline

↓

pgvector Retrieval

↓

Gemini

↓

Python AI Service

↓

Laravel

↓

ReactJS

↓

User

---

# 15. PDF Upload Architecture

When a user uploads an AME PDF:

Student
│
▼
ReactJS
│
│ PDF Upload
▼
Laravel API
│
├── Validate User
│
├── Validate File
│
├── Validate Size
│
├── Generate SHA-256
│
├── Check Duplicate
│
└── Store Metadata
│
▼
Cloudflare R2
│
│ PDF Stored
▼
Laravel Queue
│
▼
Python AI Service
│
├── Detect PDF Type
│
├── Extract Text
│
├── OCR if Required
│
├── Extract Images
│
├── Extract Tables
│
├── Detect Topics
│
├── Create Chunks
│
├── Generate Embeddings
│
└── Store Embeddings
│
▼
PostgreSQL + pgvector
│
▼
Generate PDF Context
│
▼
PDF Status = READY

---

# 16. Study Mode Architecture

When the user selects:

Study from PDF

The flow is:

Student
│
▼
ReactJS
│
▼
Laravel API
│
├── Verify User
│
├── Verify PDF Access
│
└── Get Study Session
│
▼
Python AI Service
│
├── Get Current Topic
│
├── Retrieve Relevant Chunks
│
├── Build RAG Context
│
└── Send Prompt to Gemini
│
▼
Gemini
│
▼
AI Explanation
│
├── Explanation
├── Topic
└── Source References
│
▼
Laravel
│
▼
ReactJS
│
▼
Student

---

# 17. Quiz Mode Architecture

When the user selects:

Test My Skills

The flow is:

Student
│
▼
ReactJS
│
▼
Laravel API
│
├── Verify User
│
├── Verify PDF Access
│
└── Start Quiz Session
│
▼
Python AI Service
│
├── Select Current Topic
│
├── Retrieve Topic Content
│
├── Retrieve Relevant Chunks
│
├── Build RAG Context
│
└── Generate Quiz
│
▼
Gemini
│
▼
Quiz Questions
│
├── Single Choice
├── Multiple Choice
├── Text
└── Voice
│
▼
Laravel
│
▼
ReactJS
│
▼
Student

---

# 18. Topic-by-Topic Processing

The system must not generate the entire quiz for the entire PDF at once.

The process should be:

PDF
│
▼
Topics
│
├── Topic 1
│ │
│ ├── Questions
│ ├── Answers
│ └── Evaluation
│
├── Topic 2
│ │
│ ├── Questions
│ ├── Answers
│ └── Evaluation
│
├── Topic 3
│ │
│ ├── Questions
│ ├── Answers
│ └── Evaluation
│
└── Continue
│
▼
All Topics Completed
│
▼
Final Score

This architecture reduces unnecessary AI processing and allows progress to be tracked topic-by-topic.

---

# 19. RAG Architecture

The RAG pipeline will follow:

User Query
│
▼
Query Processing
│
▼
Embedding Generation
│
▼
Vector Search
│
▼
pgvector
│
▼
Relevant Chunks
│
▼
Context Construction
│
▼
Prompt
│
▼
Gemini
│
▼
AI Response

The AI should use relevant PDF content instead of sending the complete PDF to Gemini for every request.

---

# 20. Security Boundary

The system will follow this security boundary:

                   PUBLIC
                      │
                      ▼
              ReactJS Frontend
                      │
                      │ HTTPS
                      ▼
              Laravel Backend
                      │
          ┌───────────┴───────────┐
          │                       │
          ▼                       ▼
     PostgreSQL              Python AI
     + pgvector                Service
          │                       │
          │                       ▼
          │                  Gemini API
          │
          ▼
    Cloudflare R2

The following credentials must remain server-side:

- Gemini API Keys
- PostgreSQL Credentials
- Cloudflare R2 Credentials
- Internal Service Credentials

---

# 21. Internal Service Communication

The Python AI Service is not the public API for the application.

The primary communication should be:

ReactJS

↓

Laravel API

↓

Python AI Service

The ReactJS frontend should not directly call the Python AI Service.

This allows Laravel to maintain:

- Authentication
- Authorization
- User Ownership
- Business Rules

---

# 22. Database and AI Separation

The system should separate application responsibilities from AI responsibilities.

Laravel should determine:

- Which user is making the request.
- Which PDF is being accessed.
- Which topic belongs to the PDF.
- Which study session belongs to the user.
- Which quiz session belongs to the user.

Python should determine:

- How PDF content is processed.
- Which chunks are relevant.
- How the RAG context is built.
- How AI responses are generated.

This separation is important for security and maintainability.

---

# 23. Background Processing Architecture

Long-running PDF operations should use background processing.

The flow should be:

ReactJS
│
▼
Laravel API
│
▼
Create PDF Record
│
▼
Create Processing Job
│
▼
Queue
│
▼
Worker
│
▼
Python AI Service
│
▼
Process PDF
│
▼
Update Status

The user should not have to keep the upload request open while the PDF is being processed.

---

# 24. PDF Processing Status

The PDF processing lifecycle should be:

UPLOADING
│
▼
UPLOADED
│
▼
PROCESSING
│
├───────────────┐
│ │
▼ ▼
READY FAILED

The frontend should display the current status.

---

# 25. Failure Isolation

The system should isolate failures between services.

Example:

If Gemini is unavailable:

ReactJS
│
▼
Laravel
│
▼
Python AI Service
│
▼
Gemini
X
Unavailable

The system should return a controlled error.

The rest of the application should remain available.

For example:

- Dashboard can still open.
- PDF list can still open.
- Study history can still open.
- Quiz history can still open.

---

# 26. Version 1 Architecture Summary

The final Version 1 architecture is:

┌─────────────────────────────────────────────────────┐
│ STUDENT │
└─────────────────────────┬───────────────────────────┘
│
▼
┌─────────────────────────────────────────────────────┐
│ REACTJS FRONTEND │
│ │
│ Dashboard | PDF | Study | Quiz | Progress | Result │
└─────────────────────────┬───────────────────────────┘
│
│ REST API / HTTPS
▼
┌─────────────────────────────────────────────────────┐
│ LARAVEL BACKEND │
│ │
│ Auth | Authorization | Business Logic | API │
│ PDF Management | Study | Quiz | Progress │
└───────────────┬─────────────────────┬───────────────┘
│ │
│ │
▼ ▼
┌────────────────────────┐ ┌────────────────────────┐
│ POSTGRESQL │ │ PYTHON AI SERVICE │
│ │ │ │
│ Users │ │ PDF Processing │
│ PDFs │ │ OCR │
│ Topics │ │ Chunking │
│ Study │ │ Embeddings │
│ Quiz │ │ RAG │
│ Progress │ │ AI Evaluation │
│ Scores │ │ │
│ + pgvector │ └───────────┬────────────┘
└────────────────────────┘ │
▼
┌─────────────────────┐
│ GEMINI API │
│ │
│ AI Generation │
│ AI Explanation │
│ AI Evaluation │
└─────────────────────┘

┌─────────────────────────────────────────────────────┐
│ CLOUDFLARE R2 │
│ │
│ Original PDFs | Images | Voice Files │
└─────────────────────────────────────────────────────┘

---

# 27. Final Technology Responsibility Table

| Component        | Technology           | Main Responsibility          |
| ---------------- | -------------------- | ---------------------------- |
| Frontend         | ReactJS              | UI and User Interaction      |
| Backend          | Laravel              | Application Logic and APIs   |
| Backend Language | PHP                  | Laravel Development          |
| AI Service       | Python               | AI and Document Processing   |
| AI Framework     | LangChain            | RAG and AI Pipeline          |
| Database         | PostgreSQL           | Structured Data              |
| Vector Search    | pgvector             | Semantic Search              |
| Storage          | Cloudflare R2        | PDF and File Storage         |
| AI Provider      | Gemini API           | AI Generation and Evaluation |
| API              | REST                 | System Communication         |
| Queue            | Laravel Queue        | Background Processing        |
| Worker           | Laravel Queue Worker | Long-running Jobs            |
| Version Control  | Git                  | Source Code Management       |

---

# 28. Architecture Principles

The system should follow these principles.

## Principle 1 - Separation of Responsibilities

Each service should have a clearly defined responsibility.

## Principle 2 - Security First

Sensitive credentials should never be exposed to the frontend.

## Principle 3 - AI Through Backend

The frontend should never directly communicate with Gemini.

## Principle 4 - PDF-Grounded AI

AI responses related to the uploaded PDF should use the RAG pipeline.

## Principle 5 - Asynchronous Processing

Large and long-running PDF processing should run in the background.

## Principle 6 - User Data Isolation

Users should only access their own authorized data.

## Principle 7 - Independent Scaling

Laravel and Python AI processing should be capable of scaling independently.

## Principle 8 - Cost Efficiency

The system should avoid unnecessary AI requests and duplicate PDF processing.

---

# 29. Part 1 Completion Status

The following architecture decisions are now documented:

- ReactJS frontend
- Laravel/PHP backend
- Python AI service
- LangChain AI framework
- PostgreSQL database
- pgvector vector search
- Cloudflare R2 storage
- Gemini API
- REST API communication
- Laravel-based authentication
- Asynchronous PDF processing
- Laravel Queue and Worker
- ReactJS → Laravel → Python architecture
- Python → Gemini architecture
- PostgreSQL + pgvector architecture
- Private Cloudflare R2 storage
- Topic-by-topic Study Mode
- Topic-by-topic Quiz Mode
- RAG-based PDF grounding

---

End of Technical Architecture Document - Part 1
