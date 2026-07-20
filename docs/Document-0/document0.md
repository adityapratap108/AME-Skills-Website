# 00_Project_Blueprint.md

Project Name: AME AI Tutor

Version: 1.0

Status: Approved Blueprint

---

# 1. Project Overview

AME AI Tutor is an AI-powered web application for Aircraft Maintenance Engineering (AME) students.

Students can upload their own AME PDFs.

The AI will understand the PDF, divide it into topics, teach every topic, conduct quizzes, evaluate answers, track progress, and generate reports.

This application is NOT a PDF chatbot.

It is an AI Learning Platform.

---

# 2. Main Modules

Module 1

Authentication

---

Student Registration

Login

Google Login

Forgot Password

Email Verification

---

Module 2

Dashboard

---

Student Details

Uploaded PDFs

Study Time

Quiz Time

Average Score

Recent Activity

---

Module 3

PDF Management

---

Upload PDF

Delete PDF

View PDF

PDF History

Search PDFs

---

Module 4

AI Processing

---

Detect PDF Type

Extract Text

OCR

Extract Images

Extract Tables

Topic Detection

Chunking

Embeddings

Vector Storage

Summary Generation

---

Module 5

Study Mode

---

Explain Topic

Examples

Revision

Next Topic

---

Module 6

Quiz Mode

---

MCQ

Multiple Correct

Text Answer

Voice Answer

Evaluation

Next Topic

---

Module 7

Reports

---

Overall Score

Topic Scores

Weak Topics

Strong Topics

Study Time

Quiz Time

Completion %

---

Module 8

Admin Panel

---

Users

PDFs

Analytics

AI Usage

Logs

---

# 3. User Workflow

Register

↓

Login

↓

Dashboard

↓

Upload PDF

↓

PDF Processing

↓

PDF Ready

↓

Study Mode

OR

Quiz Mode

↓

Topic Completed

↓

Next Topic

↓

Final Report

---

# 4. PDF Workflow

Upload PDF

↓

Generate SHA-256 Hash

↓

Duplicate Check

↓

Store Original PDF

↓

Detect PDF Type

↓

Extract Data

↓

Create Topics

↓

Chunk Content

↓

Generate Embeddings

↓

Store in Vector Database

↓

Generate Summary

↓

Ready

---

# 5. Study Workflow

Select PDF

↓

Load Topics

↓

Topic 1

↓

AI Explanation

↓

Student Questions

↓

Topic Summary

↓

Next Topic

↓

Completed

---

# 6. Quiz Workflow

Select PDF

↓

Select Topic

↓

Generate Questions

↓

MCQ

↓

Text Question

↓

Voice Question

↓

Evaluation

↓

Topic Score

↓

Next Topic

↓

Final Score

---

# 7. Voice Workflow

AI Question

↓

Student Speaks

↓

Speech to Text

↓

AI Evaluation

↓

Score

↓

Feedback

↓

Next Question

---

# 8. AI Workflow

User Request

↓

Retrieve Relevant Chunks

↓

Send Context to Gemini

↓

Generate Response

↓

Return Response

---

# 9. Development Architecture

Frontend

↓

ReactJS

↓

Laravel API

↓

Python AI Service

↓

Gemini API

↓

PostgreSQL

↓

pgvector

↓

Cloudflare R2

---

# 10. Exact Tech Stack

Frontend

ReactJS

Vite

TypeScript

Tailwind CSS

shadcn/ui

React Router

TanStack Query

Axios

React Hook Form

Zod

---

Backend

Laravel 12

PHP 8.4+

Laravel Sanctum

Laravel Socialite

Laravel Queue

Redis

---

AI Service

Python 3.12

FastAPI

PyMuPDF

pdfplumber

Docling

Unstructured

OpenCV

Pillow

Tesseract OCR

Camelot

Google GenAI SDK

---

Database

PostgreSQL

pgvector

---

Storage

Cloudflare R2

---

Deployment

Docker

Nginx

GitHub

---

# 11. Development Order

Phase 1

Authentication

Phase 2

Dashboard

Phase 3

PDF Upload

Phase 4

Python AI Service

Phase 5

RAG

Phase 6

Study Mode

Phase 7

Quiz Mode

Phase 8

Voice

Phase 9

Reports

Phase 10

Admin Panel

Phase 11

Testing

Phase 12

Deployment

---

# 12. Project Folder

ame-ai-tutor/

docs/

frontend/

backend/

ai-service/

database/

docker/

README.md

---

End of Blueprint
