# 01_Software_Requirement_Specification.md

**Project Name:** AME AI Tutor

**Document ID:** SRS-01

**Version:** 1.0

**Status:** Draft

**Author:** Adityapratap Singh

**Last Updated:** July 2026

---

# 1. Project Overview

## 1.1 Introduction

AME AI Tutor is an AI-powered web application designed specifically for Aircraft Maintenance Engineering (AME) students.

The main goal of this application is to help students study from their own PDF notes and books instead of searching information manually.

The application will not behave like a normal PDF chatbot.

Instead, it will work as an intelligent tutor that understands the uploaded PDF, divides it into topics, teaches every topic one by one, and evaluates the student's understanding through quizzes.

---

## 1.2 Project Goal

The goal of this project is to build an AI-based learning platform where students can:

- Upload AME-related PDF files.
- Study every topic in a structured way.
- Test their knowledge using AI-generated quizzes.
- Track their learning progress.
- Improve weak topics using AI feedback.

---

## 1.3 Project Type

**Application Type**

AI Powered Learning Platform

**Target Users**

Aircraft Maintenance Engineering (AME) Students

**Platform**

Web Application

**Supported Devices**

- Desktop
- Laptop
- Tablet
- Mobile Browser

---

# 2. Project Objectives

The application should achieve the following objectives.

### OBJ-001

Allow students to upload their own AME PDF files.

---

### OBJ-002

Automatically understand the uploaded PDF.

---

### OBJ-003

Break the PDF into meaningful topics.

The system should not simply divide the PDF by pages or characters.

Topics should be identified based on document headings and content.

---

### OBJ-004

Provide an AI Study Mode.

The AI should explain one topic at a time instead of explaining the entire PDF together.

---

### OBJ-005

Provide an AI Quiz Mode.

The AI should generate quizzes only from the uploaded PDF.

---

### OBJ-006

Track student learning progress.

---

### OBJ-007

Maintain complete learning history.

---

### OBJ-008

Provide a final performance report after completing all topics.

---

# 3. Project Scope

The application will only support AME-related study material.

Examples:

- Aircraft Systems
- Electrical Systems
- Airframe
- Powerplant
- Avionics
- Human Factors
- Maintenance Practices
- DGCA Notes
- AME Study Notes

The application will not provide answers from random internet sources unless that feature is added in a future version.

All study and quiz content should be generated primarily from the uploaded PDF.

---

# 4. User Roles

The application will have two user roles.

---

## 4.1 Student

A student can:

- Create an account.
- Login.
- Upload PDFs.
- Study PDFs.
- Take quizzes.
- View reports.
- View learning history.
- Manage profile.

---

## 4.2 Admin

The admin can:

- View all users.
- View uploaded PDFs.
- View platform analytics.
- Manage users.
- Remove inappropriate PDFs if required.
- Monitor AI usage.

---

# 5. High-Level System Flow

The overall application flow will be:

Student Login

↓

Dashboard

↓

Upload PDF

↓

PDF Processing

↓

PDF Ready

↓

Choose Mode

↓

Study Mode
OR
Quiz Mode

↓

Topic by Topic Learning

↓

Completion

↓

Final Report

---

# 6. High-Level Features

The application will contain the following modules.

---

## Module 1

User Authentication

Purpose

Manage user accounts securely.

Main Features

- Register
- Login
- Logout
- Forgot Password
- Update Profile

---

## Module 2

Dashboard

Purpose

Provide complete overview of user activity.

Dashboard should display:

- User Name
- Profile Photo
- Total PDFs
- Total Study Time
- Total Quiz Time
- Average Score
- Recently Uploaded PDFs
- Recent Activity

---

## Module 3

PDF Management

Purpose

Allow students to upload and manage PDF files.

Main Features

- Upload PDF
- View PDF Details
- Delete PDF
- Reopen Previous PDF
- Search Uploaded PDFs

---

## Module 4

AI Study Mode

Purpose

Teach every topic step by step.

The AI should:

- Explain one topic.
- Give examples.
- Ask if the student understood.
- Move to the next topic only after finishing the current one.

---

## Module 5

AI Quiz Mode

Purpose

Evaluate student understanding.

The AI should generate:

- Single Choice MCQs
- Multiple Choice Questions
- Text Answer Questions
- Voice Answer Questions

Questions should be generated topic by topic.

The next topic should start only after the current topic evaluation is completed.

---

## Module 6

Progress Tracking

Purpose

Track complete student learning journey.

The system should store:

- Study Time
- Quiz Time
- Topic Completion
- Quiz Scores
- Overall Progress

---

## Module 7

Reports

Purpose

Generate student performance reports.

Reports should include:

- Overall Score
- Topic-wise Score
- Weak Topics
- Strong Topics
- Study Time
- Quiz Time
- Completion Percentage

---

# 7. Functional Requirements (High Level)

| ID     | Requirement          |
| ------ | -------------------- |
| FR-001 | User Registration    |
| FR-002 | User Login           |
| FR-003 | User Logout          |
| FR-004 | Upload PDF           |
| FR-005 | Process PDF          |
| FR-006 | Detect Topics        |
| FR-007 | Generate PDF Summary |
| FR-008 | Start Study Mode     |
| FR-009 | Start Quiz Mode      |
| FR-010 | Generate Quiz        |
| FR-011 | Evaluate Answers     |
| FR-012 | Save Progress        |
| FR-013 | Show Dashboard       |
| FR-014 | Show Reports         |
| FR-015 | Manage User Profile  |

---

# 8. Non-Functional Requirements (High Level)

| ID      | Requirement                |
| ------- | -------------------------- |
| NFR-001 | Fast PDF Upload            |
| NFR-002 | Secure User Authentication |
| NFR-003 | Secure PDF Storage         |
| NFR-004 | Responsive Design          |
| NFR-005 | Scalable Architecture      |
| NFR-006 | Reliable AI Responses      |
| NFR-007 | Proper Error Handling      |
| NFR-008 | Activity Logging           |

---

# 9. Project Boundaries

The first version of the application will only support:

- AME PDFs
- English Language PDFs
- PDF File Format

The following are outside the scope of Version 1:

- Multiple Languages
- Internet-based AI Search
- Live Video Classes
- Teacher Accounts
- Mobile Application

---

# 10. Pending Decisions

The following requirements are intentionally left pending and will be finalized in later parts of this document.

PD-001

Maximum PDF Size

Status

Pending

---

PD-002

Maximum Number of PDFs per User

Status

Pending

---

PD-003

Quiz Passing Criteria

Status

Pending

---

PD-004

Voice Answer Time Limit

Status

Pending

---

PD-005

Allowed PDF Types

Status

Pending

---

# 11. Approval Status

Document Status: Final Draft

Approval Status: Approved

---

End of SRS Part 1
