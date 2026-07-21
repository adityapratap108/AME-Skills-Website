# 01_Software_Requirement_Specification.md

## Part 4 - PDF Processing, AI Pipeline and RAG Requirements

**Project Name:** AME AI Tutor

**Document ID:** SRS-01-Part-4

**Version:** 1.0

**Status:** Draft

---

# 1. PDF Processing Overview

The system must process every uploaded PDF before the user can use Study Mode or Quiz Mode.

The PDF processing pipeline should:

1. Receive the uploaded PDF.
2. Validate the PDF.
3. Store the original PDF.
4. Detect the PDF type.
5. Extract text.
6. Detect scanned pages where required.
7. Apply OCR where required.
8. Extract images.
9. Extract tables where possible.
10. Detect document structure.
11. Detect topics and subtopics.
12. Create meaningful content chunks.
13. Generate embeddings.
14. Store vectors in the vector database.
15. Store metadata and source references.
16. Mark the PDF as Ready.

The overall process should preserve the relationship between the original PDF content and the processed AI data.

---

# 2. PDF Processing Lifecycle

The PDF processing lifecycle should be:

Upload PDF

↓

Validate PDF

↓

Calculate SHA-256 Hash

↓

Check Duplicate

↓

Store Original PDF

↓

Detect PDF Type

↓

Extract Content

↓

OCR if Required

↓

Extract Images

↓

Extract Tables

↓

Detect Structure

↓

Detect Topics

↓

Create Chunks

↓

Generate Embeddings

↓

Store Chunks and Vectors

↓

Create PDF Context

↓

Mark PDF as Ready

---

# 3. PDF Validation

## FR-071 - Validate Uploaded PDF

Before processing begins, the system must validate the uploaded PDF.

The system must check:

- File extension
- MIME type
- File size
- PDF validity
- Corruption
- Password protection

The maximum allowed file size is 100 MB.

If the file exceeds 100 MB, the system must reject it.

The user should see:

> This PDF exceeds the maximum allowed size of 100 MB. Please split the PDF into smaller parts and upload them separately.

---

# 4. SHA-256 Duplicate Detection

## FR-072 - Generate PDF Hash

The system must generate a SHA-256 hash for every uploaded PDF.

The hash should be generated from the actual file content.

The system should use the hash to identify duplicate PDFs.

The file name should not be used as the primary duplicate detection method.

---

# 5. Duplicate PDF Processing

## FR-073 - Handle Duplicate PDF

If a PDF with the same SHA-256 hash has already been processed:

The system should not process the same PDF again.

The system should reuse the existing processed data where possible.

The system should create the appropriate user-level relationship with the already processed PDF data.

The AI processing pipeline should not unnecessarily repeat:

- Text extraction
- OCR
- Image extraction
- Table extraction
- Chunking
- Embedding generation

---

# 6. Original PDF Storage

## FR-074 - Store Original PDF

The original uploaded PDF should be stored in Cloudflare R2.

The system should maintain a secure reference to the stored file.

The original PDF should not be stored directly inside the PostgreSQL database.

The database should store metadata and the storage reference.

---

# 7. PDF Type Detection

## FR-075 - Detect PDF Type

The system should automatically determine whether the PDF is:

1. Digital PDF
2. Scanned PDF
3. Mixed PDF

A Mixed PDF may contain both:

- Digital text pages
- Scanned image pages

The system should process each page according to its content when required.

---

# 8. Digital PDF Processing

## FR-076 - Extract Digital PDF Content

For PDFs containing machine-readable text, the system should extract:

- Text
- Headings
- Subheadings
- Paragraphs
- Lists
- Page numbers
- Tables where possible
- Images
- Figure references

The extracted content should retain its relationship with the original page.

---

# 9. Scanned PDF Processing

## FR-077 - OCR Processing

For scanned pages, the system should use OCR to extract text.

OCR processing should support:

- Normal pages
- Rotated pages
- Different page orientations

The extracted OCR text should be linked to:

- PDF
- Page number
- Topic
- Section where available

---

# 10. Rotated Page Handling

## FR-078 - Handle Rotated Pages

The PDF processing system should detect page rotation where possible.

Before OCR processing, the system should correctly orient the page.

The goal is to ensure that rotated text can be correctly recognized.

The original page orientation should not be changed in the original stored PDF.

Only the processing copy or OCR pipeline should apply required orientation corrections.

---

# 11. Image Extraction

## FR-079 - Extract Images

The system should extract relevant images from the PDF.

Images may include:

- Aircraft diagrams
- Technical diagrams
- Figures
- Schematics
- Illustrations
- Photographs

Each extracted image should retain metadata such as:

- PDF ID
- Page Number
- Topic ID
- Section
- Figure Number, if available
- Original image position where possible

---

# 12. Figure and Diagram Handling

## FR-080 - Preserve Figure References

If the PDF contains references such as:

- Figure 4.1
- Figure 4.2
- Diagram 3
- Illustration 5

The system should preserve these references.

The AI should be able to identify the relationship between:

- Figure
- Figure Label
- Page
- Topic
- Related Text

Example:

Figure 4.2

PDF: Aircraft Electrical Systems

Page: 45

Topic: Generator System

Image: Extracted Figure

---

# 13. Table Extraction

## FR-081 - Extract Tables

The system should attempt to identify and extract tables.

Extracted table information should remain linked to:

- PDF
- Page
- Topic
- Section

Where possible, table structure should be preserved.

The system should avoid treating every table as plain unstructured text if structured extraction is available.

---

# 14. Content Structure Detection

## FR-082 - Detect Document Structure

The system should identify document structure such as:

- Chapters
- Sections
- Subsections
- Headings
- Subheadings
- Paragraphs
- Lists

The structure should be used to create logical topics and content chunks.

---

# 15. Topic Detection

## FR-083 - Detect Topics

The AI pipeline should identify logical topics from the processed PDF.

Topic detection should consider:

- Headings
- Subheadings
- Chapters
- Sections
- Content similarity
- Semantic meaning
- Document structure

Page numbers alone should not be used to define topics.

---

# 16. Topic Hierarchy

## FR-084 - Store Topic Hierarchy

The system should support a hierarchical structure.

Example:

Aircraft Electrical Systems

- 1. Electrical Fundamentals
  - 1.1 Voltage
  - 1.2 Current
  - 1.3 Resistance

- 2. Batteries
  - 2.1 Battery Types
  - 2.2 Battery Maintenance

- 3. Generators
  - 3.1 Generator Operation
  - 3.2 Generator Maintenance

The system should preserve the relationship between parent topics and subtopics where detected.

---

# 17. Content Chunking

## FR-085 - Create Content Chunks

The system should divide extracted content into meaningful chunks.

Chunks should not be created using only a fixed number of characters.

The chunking process should consider:

- Topic
- Section
- Paragraph boundaries
- Heading boundaries
- Semantic meaning

The goal is to keep related information together.

---

# 18. Chunk Metadata

Each chunk should contain metadata such as:

- PDF ID
- Topic ID
- Section ID, where available
- Page Number
- Start Page
- End Page
- Content Type
- Source Reference
- Figure Reference, where applicable
- Table Reference, where applicable

Content Type may include:

- Text
- Table
- Image Description
- OCR Text

---

# 19. Chunk Overlap

## FR-086 - Context Preservation

The chunking system should preserve enough contextual overlap between related chunks where required.

The purpose is to avoid losing context when information continues from one chunk to another.

The exact chunk size and overlap values will be implementation-level configuration.

---

# 20. Embedding Generation

## FR-087 - Generate Embeddings

The system should generate vector embeddings for searchable content chunks.

Embeddings should be generated for content that needs semantic retrieval.

The embedding process should support:

- Text chunks
- Relevant table content
- Relevant image descriptions or extracted visual context where available

---

# 21. Vector Storage

## FR-088 - Store Embeddings

The generated embeddings should be stored in PostgreSQL using pgvector.

Each vector should remain linked to its original content chunk.

The relationship should allow the system to identify:

- PDF
- Topic
- Section
- Page
- Source
- Content

---

# 22. RAG Pipeline

## FR-089 - Retrieval-Augmented Generation

The AI system should use a Retrieval-Augmented Generation architecture.

The basic flow should be:

User Query

↓

Identify Current PDF

↓

Identify Current Topic

↓

Create Query Embedding

↓

Search Relevant Vectors

↓

Retrieve Relevant Chunks

↓

Apply Topic and PDF Filtering

↓

Build AI Context

↓

Send Context to Gemini

↓

Generate Response

↓

Return Answer

---

# 23. PDF-Level Filtering

## FR-090 - Restrict Retrieval to Current PDF

When the user is studying or testing a specific PDF, the retrieval system should prioritize content from that PDF.

The AI should not retrieve content from another user's PDF.

---

# 24. Topic-Level Filtering

## FR-091 - Restrict Retrieval to Current Topic

During Study Mode or Quiz Mode, the system should prioritize the current topic.

For example:

Current Topic:

Generator Systems

The retrieval system should prioritize chunks belonging to:

Generator Systems

It should not unnecessarily retrieve unrelated content from:

- Batteries
- Hydraulic Systems
- Fuel Systems

---

# 25. Context Retrieval

## FR-092 - Retrieve Relevant Context

The RAG system should retrieve the most relevant content for the current task.

For Study Mode, retrieval should support:

- Topic explanation
- User questions
- Technical term explanations
- Figure explanations
- Table explanations

For Quiz Mode, retrieval should support:

- Question generation
- Answer evaluation
- Correct answer generation
- Explanation generation

---

# 26. Source Reference Retrieval

## FR-093 - Retrieve Source Metadata

Whenever relevant content is retrieved, the system should retain its source metadata.

The AI response should be able to reference:

- PDF Name
- Page Number
- Section
- Topic
- Figure
- Table

The source information should be available to the frontend.

---

# 27. Study Mode RAG Flow

The Study Mode AI flow should be:

User Opens Topic

↓

Retrieve Topic Content

↓

Retrieve Relevant Chunks

↓

Retrieve Related Images and Tables

↓

Build Context

↓

Send Context to Gemini

↓

Generate Explanation

↓

Attach Source References

↓

Display Content

---

# 28. Study Question RAG Flow

When the user asks a question during Study Mode:

User Question

↓

Identify Current PDF

↓

Identify Current Topic

↓

Create Query Embedding

↓

Retrieve Relevant Chunks

↓

Retrieve Related Source Information

↓

Build Context

↓

Send Context to Gemini

↓

Generate Answer

↓

Display Answer + Sources

---

# 29. Quiz Generation RAG Flow

For each topic:

Select Topic

↓

Retrieve Topic Content

↓

Retrieve Important Chunks

↓

Retrieve Related Images and Tables

↓

Build Quiz Generation Context

↓

Send Context to Gemini

↓

Generate Questions

↓

Validate Questions

↓

Store Questions

↓

Start Quiz

---

# 30. Quiz Answer Evaluation RAG Flow

For Text and Voice Answers:

Student Answer

↓

Convert Voice to Text if Required

↓

Identify Current Question

↓

Retrieve Question Context

↓

Retrieve Relevant PDF Chunks

↓

Build Evaluation Context

↓

Send Context to Gemini

↓

Evaluate Answer

↓

Generate Score

↓

Generate Explanation

↓

Store Result

---

# 31. Voice Processing

## FR-094 - Voice Answer Processing

Voice answers should be processed using:

Voice Recording

↓

Speech-to-Text

↓

Transcribed Answer

↓

AI Evaluation

The system should store the transcription required for evaluation and quiz history.

The original voice recording may be stored according to the application's storage requirements.

---

# 32. AI Context Rules

## FR-095 - Context Grounding

The AI should receive relevant retrieved context before generating:

- Study Explanations
- Study Answers
- Quiz Questions
- Text Answer Evaluations
- Voice Answer Evaluations

The system should avoid sending the entire PDF to the AI unnecessarily.

---

# 33. Hallucination Control

## FR-096 - Reduce Unsupported AI Responses

The AI pipeline should instruct the model to prioritize retrieved PDF content.

If sufficient information is not available in the retrieved context, the AI should clearly indicate that the answer cannot be confirmed from the uploaded PDF.

The system should avoid presenting unsupported information as if it came directly from the PDF.

---

# 34. AI Response Validation

## FR-097 - Validate AI Output

AI-generated output should be validated before being returned to the user where required.

Validation should check:

- Expected response format
- Required fields
- Question structure
- Answer options
- Correct answer
- Difficulty
- Source references

Invalid AI output should be rejected or regenerated.

---

# 35. Structured Quiz Output

Quiz question generation should return structured data containing:

- Question
- Question Type
- Options, if applicable
- Correct Answer
- Explanation
- Difficulty
- Source Reference
- Topic ID

For multiple-choice questions, multiple correct answers should be supported.

---

# 36. AI Service Responsibilities

The Python AI Service should be responsible for AI-related processing such as:

- PDF content processing
- OCR coordination
- Text extraction coordination
- Image processing coordination
- Table processing coordination
- Topic detection
- Content chunking
- Embedding generation
- Vector retrieval
- RAG context creation
- Gemini API communication
- Quiz generation
- Answer evaluation

Laravel should remain responsible for application-level business logic and API communication with the React frontend.

---

# 37. Laravel and Python Communication

Laravel and the Python AI Service should communicate through internal APIs.

Basic flow:

React Frontend

↓

Laravel API

↓

Python AI Service

↓

AI Processing

↓

Python AI Service

↓

Laravel API

↓

React Frontend

Laravel should manage user authentication and application-level authorization.

The Python AI Service should not directly manage the application's user authentication system.

---

# 38. Processing Failure

## FR-098 - Handle AI Processing Failure

If PDF processing fails, the system should:

- Mark processing status as Failed.
- Store an appropriate error status.
- Prevent Study Mode from starting.
- Prevent Quiz Mode from starting.
- Display a user-friendly message.

Example:

> We could not process this PDF. Please try again later.

---

# 39. Processing Retry

The system should support retrying failed PDF processing.

A retry should attempt to process the PDF again.

The original uploaded PDF should not need to be uploaded again unless the file itself is invalid.

---

# 40. PDF Ready State

## FR-099 - Mark PDF as Ready

A PDF should be marked as Ready only after the required processing steps have completed successfully.

The Ready state means:

- PDF is stored.
- Text is processed.
- Required OCR is completed.
- Required images are processed.
- Required tables are processed.
- Topics are identified.
- Chunks are created.
- Embeddings are stored.
- Required metadata is available.

Only a Ready PDF can be used for:

- Study Mode
- Quiz Mode

---

# 41. Processing Status

The system should maintain processing states:

- Uploaded
- Processing
- Text Extraction
- OCR Processing
- Image Processing
- Table Processing
- Topic Detection
- Chunking
- Embedding
- Ready
- Failed

The frontend should display a simplified status to the user.

Example:

Processing PDF...

The detailed internal processing status may be stored for system use.

---

# 42. Security Requirements

The AI system must ensure that one user's PDF content is not exposed to another user.

The retrieval system must filter content by authorized PDF ownership or access permissions.

The AI service must not expose internal API credentials to the React frontend.

Gemini API keys must remain server-side.

---

# 43. API Key Security

Gemini API keys must:

- Never be stored in React frontend code.
- Never be exposed in browser network responses.
- Never be committed to Git repositories.
- Be stored using environment variables or secure server-side configuration.

The Python AI Service should be responsible for communicating with Gemini.

---

# 44. AI Cost Control

The system should avoid unnecessary AI API calls.

The system should:

- Reuse processed PDF data.
- Reuse embeddings.
- Avoid reprocessing duplicate PDFs.
- Retrieve only relevant chunks.
- Avoid sending the entire PDF to Gemini.
- Store generated quiz questions where appropriate.

---

# 45. Acceptance Criteria

The PDF AI Processing and RAG system will be considered complete when:

- PDF type can be detected.
- Digital PDF text can be extracted.
- Scanned PDF pages can be processed using OCR.
- Rotated pages can be handled.
- Images can be extracted.
- Tables can be processed.
- Figure references can be preserved.
- Topics can be detected.
- Topic hierarchy can be stored.
- Content can be chunked.
- Chunk metadata can be stored.
- Embeddings can be generated.
- Embeddings can be stored in pgvector.
- RAG retrieval works.
- Retrieval can be filtered by PDF.
- Retrieval can be filtered by Topic.
- Study Mode can use RAG.
- Quiz Generation can use RAG.
- Answer Evaluation can use RAG.
- Source references can be returned.
- AI output can be validated.
- Failed processing can be detected.
- Failed processing can be retried.
- PDF is marked Ready only after required processing is complete.
- Gemini API keys remain server-side.
- Users cannot access another user's PDF content through AI retrieval.

---

# 46. Locked Architecture Components Used in This Part

The following components are part of the already approved architecture:

- ReactJS
- Laravel
- Python AI Service
- PostgreSQL
- pgvector
- Cloudflare R2
- Gemini API

This document does not introduce a new architecture.

---

End of SRS Part 4
