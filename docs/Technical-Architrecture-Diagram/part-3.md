# 03_PDF_Processing_and_RAG_Pipeline.md

# Part 3 - Exact PDF Processing and RAG Pipeline

**Project Name:** AME AI Tutor

**Document ID:** TAD-01-Part-3

**Version:** 1.0

**Status:** Draft

**Depends On:** TAD-01-Part-1 and TAD-01-Part-2

---

# 1. Purpose

This document defines the exact PDF processing and RAG pipeline for the AME AI Tutor application.

The goal of this pipeline is to convert an uploaded AME-related PDF into structured, searchable, and AI-readable content.

The pipeline must support:

- Digital PDFs
- Scanned PDFs
- Mixed PDFs
- Rotated pages
- Text
- Tables
- Images
- Figures
- Captions
- Page numbers
- Sections
- Topics
- Semantic chunks
- Embeddings
- Vector search
- RAG
- Source references

The pipeline will be used by both:

- Study Mode
- Quiz Mode

---

# 2. Locked AI Processing Architecture

The Version 1 AI processing architecture is:

```text
                    USER UPLOADS PDF
                           │
                           ▼
                    REACTJS FRONTEND
                           │
                           ▼
                    LARAVEL BACKEND
                           │
                  Validate PDF File
                           │
                           ▼
                    CLOUDFLARE R2
                           │
                           ▼
                    LARAVEL QUEUE
                           │
                           ▼
                 PYTHON AI SERVICE
                           │
                           ▼
                  PDF TYPE DETECTION
                           │
              ┌────────────┼────────────┐
              │            │            │
              ▼            ▼            ▼
           DIGITAL       SCANNED       MIXED
              │            │            │
              ▼            ▼            ▼
        TEXT PARSING      OCR       TEXT + OCR
              │            │            │
              └────────────┼────────────┘
                           │
                           ▼
                 IMAGE / FIGURE EXTRACTION
                           │
                           ▼
                     TABLE EXTRACTION
                           │
                           ▼
                 DOCUMENT STRUCTURE
                           │
                           ▼
                    TOPIC DETECTION
                           │
                           ▼
                   SEMANTIC CHUNKING
                           │
                           ▼
                    EMBEDDING MODEL
                           │
                           ▼
                  POSTGRESQL + PGVECTOR
                           │
                           ▼
                     RAG PIPELINE
                           │
              ┌────────────┴────────────┐
              │                         │
              ▼                         ▼
          STUDY MODE                 QUIZ MODE
              │                         │
              ▼                         ▼
        AI EXPLANATION             AI QUESTIONS
              │                         │
              ▼                         ▼
          SOURCES                  EVALUATION
              │                         │
              └────────────┬────────────┘
                           │
                           ▼
                       REACTJS
```

This architecture is the primary Version 1 pipeline.

---

# 3. Exact Technology Stack

The following tools are recommended for the Version 1 implementation.

## 3.1 Backend

```text
PHP
Laravel
Laravel Sanctum
Laravel Queue
Laravel HTTP Client
```

Laravel is responsible for application logic and communication with the AI Service.

---

## 3.2 AI Service

```text
Python
FastAPI
Uvicorn
Pydantic
```

FastAPI will expose private internal endpoints to Laravel.

---

## 3.3 PDF Processing

The primary PDF processing libraries are:

```text
PyMuPDF
pdfplumber
```

PyMuPDF will be the primary PDF processing library.

pdfplumber will be used where structured text or table extraction is more suitable.

---

## 3.4 OCR

The primary OCR solution is:

```text
Tesseract OCR
```

Python integration:

```text
pytesseract
```

Image processing:

```text
Pillow
```

PDF page rendering:

```text
PyMuPDF
```

The Version 1 pipeline will use local OCR rather than depending on a paid cloud OCR service.

---

## 3.5 Table Processing

Recommended tools:

```text
pdfplumber
Camelot
```

The system should first attempt structured table extraction.

If a table cannot be extracted reliably, the table can be preserved as page content and referenced through the page source.

---

## 3.6 Image Processing

Recommended tools:

```text
PyMuPDF
Pillow
```

PyMuPDF will extract embedded PDF images.

Pillow will be used for image processing and normalization where required.

---

## 3.7 Embeddings

The embedding provider should be selected separately from the main Gemini generation model.

The Version 1 architecture should support a configurable embedding provider.

The embedding layer should not be tightly coupled to the Gemini generation layer.

---

## 3.8 Vector Database

Version 1 will use:

```text
PostgreSQL
+
pgvector
```

A separate vector database is not required for Version 1.

---

## 3.9 Object Storage

The application will use:

```text
Cloudflare R2
```

R2 will store:

- Original PDFs
- Extracted images
- Extracted figures
- Voice recordings

---

## 3.10 AI Model

The application will use:

```text
Google Gemini API
```

Gemini will be used for:

- Context generation
- Study explanations
- Quiz generation
- Text answer evaluation
- Voice answer evaluation

---

# 4. PDF Processing Pipeline

The complete processing pipeline is:

```text
PDF Upload
    │
    ▼
File Validation
    │
    ▼
Store Original PDF
    │
    ▼
Create Processing Job
    │
    ▼
Load PDF
    │
    ▼
Analyze Pages
    │
    ▼
Detect Page Type
    │
    ├───────────────┐
    │               │
    ▼               ▼
Digital Page    Scanned Page
    │               │
    ▼               ▼
Text Extract    Render Page
    │               │
    │               ▼
    │             OCR
    │               │
    │               ▼
    └───────┬───────┘
            │
            ▼
      Normalize Content
            │
            ▼
    Extract Images
            │
            ▼
    Extract Tables
            │
            ▼
   Detect Document Structure
            │
            ▼
      Detect Topics
            │
            ▼
       Create Chunks
            │
            ▼
      Generate Embeddings
            │
            ▼
        Store Vectors
            │
            ▼
       Store Metadata
            │
            ▼
        Mark READY
```

The processing pipeline should be executed asynchronously using Laravel Queue.

---

# 5. PDF File Validation

Before processing starts, Laravel must validate:

- File extension
- MIME type
- File size
- Upload ownership
- Storage availability

The maximum PDF size for Version 1 is:

```text
100 MB
```

If the PDF exceeds 100 MB, the system should not process it.

The user should see a message similar to:

```text
This PDF is larger than 100 MB.

Please split the PDF into multiple smaller PDF files and upload them separately.
```

The system should not silently reject the file.

---

# 6. PDF Loading

The Python AI Service receives the PDF reference from Laravel.

The AI Service should retrieve the PDF from R2 using a secure server-side mechanism.

The PDF should then be loaded using PyMuPDF.

Conceptually:

```text
R2 PDF
  │
  ▼
Python AI Service
  │
  ▼
PyMuPDF
  │
  ▼
PDF Document Object
```

The Python Service should process the PDF page-by-page.

---

# 7. Page-Level Processing

Each PDF page should be treated as an individual processing unit.

For every page, the system should collect:

```text
Document ID

Page Number

Page Width

Page Height

Page Rotation

Extracted Text

Text Length

Image Count

Table Count
```

Example:

```json
{
  "document_id": 123,
  "page_number": 42,
  "rotation": 90,
  "text_length": 2450,
  "image_count": 2,
  "table_count": 1
}
```

---

# 8. Digital PDF Detection

A page should be considered digital if meaningful text can be extracted directly from the PDF.

The basic logic is:

```text
PDF Page
   │
   ▼
Extract Text
   │
   ▼
Is Meaningful Text Available?
   │
   ├── YES ──> Digital Page
   │
   └── NO ───> Possible Scanned Page
```

The system should not classify a page as digital merely because a few characters are present.

For example, a scanned page may contain:

```text
Page Number: 42
```

but still be a scanned document.

Therefore, the system should consider:

- Total extracted character count
- Number of words
- Text density
- Whether the extracted text is meaningful
- Whether text covers a reasonable portion of the page

---

# 9. Scanned PDF Detection

If direct extraction returns little or no meaningful text, the page should be considered a candidate for OCR.

Flow:

```text
Page
 │
 ▼
Direct Text Extraction
 │
 ▼
Insufficient Text
 │
 ▼
Render Page as Image
 │
 ▼
OCR
 │
 ▼
OCR Text Available?
 │
 ├── YES ──> Scanned Page
 │
 └── NO ───> Image-Only / Unreadable Page
```

The system should record the result for later troubleshooting.

---

# 10. Mixed PDF Detection

A PDF can contain:

```text
Digital Pages
+
Scanned Pages
```

The system should process each page independently.

Example:

```text
Page 1 → Digital
Page 2 → Digital
Page 3 → Scanned
Page 4 → Digital
Page 5 → Scanned
```

The overall PDF classification becomes:

```text
All Digital
    → Digital PDF

All Scanned
    → Scanned PDF

Combination
    → Mixed PDF
```

This approach avoids unnecessary OCR processing.

---

# 11. Exact OCR Pipeline

For scanned pages:

```text
PDF Page
    │
    ▼
PyMuPDF
    │
    ▼
Render Page to Image
    │
    ▼
Check Rotation
    │
    ▼
Normalize Orientation
    │
    ▼
Pillow Image Processing
    │
    ▼
Tesseract OCR
    │
    ▼
Extract Text
    │
    ▼
Store OCR Text
    │
    ▼
Attach Page Metadata
```

The OCR text should retain:

- Document ID
- Page number
- Section
- Topic
- OCR status

---

# 12. OCR Image Resolution

OCR quality depends heavily on image resolution.

For Version 1, the system should render scanned PDF pages at an OCR-friendly resolution.

The target should generally be around:

```text
300 DPI
```

The exact DPI may be adjusted later based on performance and processing cost.

Higher resolution may improve OCR quality but increases:

- Processing time
- Memory usage
- Storage usage

---

# 13. OCR Text Confidence

Where Tesseract provides confidence information, the system should store it.

Example:

```json
{
  "page_number": 42,
  "ocr_confidence": 87.4
}
```

Low-confidence OCR pages can be marked internally for possible review.

The system does not need to expose OCR confidence to the student in Version 1 unless required.

---

# 14. Rotated Page Handling

The system must support:

```text
0°
90°
180°
270°
```

The pipeline should first detect page rotation.

Then:

```text
Original Page
    │
    ▼
Read Rotation Metadata
    │
    ▼
Normalize Orientation
    │
    ▼
OCR / Image Processing
```

The original PDF page number must always remain unchanged.

For example:

```text
Original PDF Page = 42

Rendered Image = Corrected Orientation

Source Reference = Page 42
```

The system should never change the source page number because of rotation.

---

# 15. Text Extraction

For digital PDFs, PyMuPDF should be used first.

The extraction process should preserve:

- Text
- Page number
- Text blocks
- Bounding boxes where available

Conceptually:

```text
PDF Page
   │
   ▼
PyMuPDF
   │
   ▼
Text Blocks
   │
   ├── Block Text
   ├── Position
   ├── Page Number
   └── Bounding Box
```

Bounding box information is useful for:

- Detecting headings
- Detecting captions
- Associating text with images
- Understanding document layout

---

# 16. Text Normalization

Extracted text should be normalized before chunking.

Normalization may include:

- Removing excessive whitespace
- Removing repeated line breaks
- Fixing broken words caused by line wrapping
- Removing repeated headers where detectable
- Removing repeated footers where detectable

However, the original extracted text should remain available for traceability.

The system should maintain:

```text
Raw Extracted Content
+
Normalized Content
```

The normalized content is used for AI processing.

The raw content is retained for debugging and source verification.

---

# 17. Header and Footer Handling

Many AME PDFs contain repeated:

- Headers
- Footers
- Page numbers
- Document titles

Repeated content should not dominate chunks.

Example:

```text
Aircraft Maintenance Engineering
Aircraft Maintenance Engineering
Aircraft Maintenance Engineering
```

If the same text appears repeatedly in the same page position, the system may classify it as a repeated header or footer.

This content can be removed from the AI processing version while preserving the original page reference.

---

# 18. Table Extraction

Tables should be processed separately from normal paragraphs.

The primary extraction process is:

```text
PDF Page
    │
    ▼
Detect Table
    │
    ▼
Extract Table
    │
    ▼
Convert to Structured Representation
    │
    ▼
Store Table Metadata
```

A table may be represented internally as:

```text
Table Title

Column A | Column B | Column C

Value 1  | Value 2  | Value 3

Value 4  | Value 5  | Value 6
```

The table should retain:

```text
Document ID

Page Number

Section

Topic

Table Number
```

---

# 19. Table Extraction Fallback

Not every PDF table can be extracted perfectly.

If structured extraction fails:

```text
Attempt Structured Extraction
        │
        ▼
Extraction Successful?
        │
        ├── YES
        │    │
        │    ▼
        │ Structured Table
        │
        └── NO
             │
             ▼
        Preserve Page Content
             │
             ▼
        Use OCR / Text Content
             │
             ▼
        Store Page Reference
```

The system should prefer accuracy over forcing an incorrect table structure.

---

# 20. Image Extraction

Images and figures should be extracted from PDF pages.

The process is:

```text
PDF Page
    │
    ▼
PyMuPDF Image Extraction
    │
    ▼
Image Binary
    │
    ▼
Normalize Image if Required
    │
    ▼
Upload to R2
    │
    ▼
Create Image Record
```

The database record should contain:

```text
Image ID

Document ID

Page Number

Storage Key

Image Type

Caption

Topic ID
```

---

# 21. Figure Detection

Figures may be:

- Embedded images
- Diagrams
- Schematics
- Charts
- Aircraft component illustrations
- Technical drawings

The system should associate extracted figures with the nearest relevant text where possible.

For example:

```text
Figure 4.2
Hydraulic System Schematic
```

The system should attempt to identify:

```text
Figure Number = 4.2

Caption = Hydraulic System Schematic

Page = 42
```

---

# 22. Figure and Caption Association

The system can use document layout information to associate a figure with its caption.

Conceptually:

```text
Page
 │
 ├── Image
 │
 ├── Caption
 │
 └── Related Paragraph
```

The association may use:

- Spatial proximity
- Text pattern matching
- Figure numbering
- Caption keywords

For example:

```text
Figure 4.2
Figure 4-2
Fig. 4.2
Fig 4.2
```

These patterns should be recognized where possible.

---

# 23. Image Storage in R2

Extracted images should be stored using predictable object keys.

Example:

```text
documents/{document_id}/images/{image_id}.png
```

Example:

```text
documents/123/images/img_42_01.png
```

The database stores the object key.

The actual binary image is stored in R2.

---

# 24. Source Mapping

Every AI-readable content unit should maintain source information.

Minimum source metadata:

```text
Document ID

Page Number
```

Recommended metadata:

```text
Document ID

Page Number

Section

Heading

Topic ID

Content Type
```

Example:

```json
{
  "document_id": 123,
  "page_number": 42,
  "section": "Hydraulic System",
  "heading": "Hydraulic Pumps",
  "topic_id": 15,
  "content_type": "text"
}
```

---

# 25. Document Structure Detection

After extraction, the system should identify the logical structure of the PDF.

Target structure:

```text
Document
 │
 ├── Chapter
 │
 │    ├── Section
 │    │
 │    │    ├── Subsection
 │    │    │
 │    │    │    ├── Topic
 │    │    │    │
 │    │    │    └── Content
```

Not every PDF will follow this exact structure.

Therefore, the system should treat this as a flexible hierarchy.

---

# 26. Heading Detection

Heading detection can use a combination of:

- Font size
- Font style
- Text position
- Numbering patterns
- Whitespace
- AI-assisted classification where required

Examples:

```text
1. Hydraulic Systems

1.1 Hydraulic Pumps

1.1.1 Types of Pumps
```

These patterns can help establish hierarchy.

---

# 27. Topic Detection

Topics should be detected after document structure extraction.

The pipeline is:

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
Candidate Topics
      │
      ▼
AI Topic Classification
      │
      ▼
Final Topic List
```

The AI should not create arbitrary topics unrelated to the document.

Topics must be grounded in the actual content.

---

# 28. Topic Metadata

Each topic should contain:

```text
Topic ID

Document ID

Topic Name

Topic Order

Parent Section

Start Page

End Page
```

Example:

```json
{
  "topic_id": 15,
  "document_id": 123,
  "topic_name": "Hydraulic Pumps",
  "topic_order": 3,
  "parent_section": "Hydraulic System",
  "start_page": 40,
  "end_page": 45
}
```

---

# 29. Topic Ordering

Topics should preserve the order in which they appear in the PDF.

Example:

```text
Topic 1 → Aircraft Structures

Topic 2 → Materials

Topic 3 → Hydraulic Systems

Topic 4 → Landing Gear
```

The quiz system should normally follow this order.

The study system may also follow this order.

---

# 30. Semantic Chunking

The document should not be split using only fixed character counts.

The preferred approach is semantic chunking.

The pipeline is:

```text
Document
    │
    ▼
Sections
    │
    ▼
Topics
    │
    ▼
Paragraphs
    │
    ▼
Semantic Units
    │
    ▼
Chunks
```

A chunk should ideally represent a coherent concept.

---

# 31. Chunk Size

Chunk size should be configurable.

The system should not hard-code a single chunk size permanently.

Initial implementation can use approximately:

```text
500 - 1000 tokens per chunk
```

with a small overlap where required.

The exact value should be tested using real AME PDFs.

---

# 32. Chunk Overlap

Chunks may have limited overlap.

Example:

```text
Chunk 1
[Content A]
[Content B]
[Content C]

Chunk 2
[Content C]
[Content D]
[Content E]
```

The overlap helps preserve context between chunks.

However, excessive overlap increases:

- Storage
- Embedding cost
- Retrieval duplication

Therefore, overlap should remain limited.

---

# 33. Chunk Metadata

Each chunk should store:

```text
Chunk ID

Document ID

Topic ID

Page Start

Page End

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
  "page_start": 42,
  "page_end": 43,
  "section": "Hydraulic System",
  "heading": "Hydraulic Pumps",
  "content_type": "text",
  "content": "Hydraulic pumps are..."
}
```

---

# 34. Chunking Special Content

Special content should not be treated exactly like normal paragraphs.

The system should identify:

```text
Text

Table

Image

Figure

Caption

OCR Text
```

A figure-related chunk may contain:

```text
Figure Number

Figure Caption

Related Text

Page Number

Image ID
```

This allows the AI to understand that an image exists and allows the frontend to display it.

---

# 35. Embedding Generation

After chunk creation:

```text
Chunk
  │
  ▼
Embedding Model
  │
  ▼
Vector
  │
  ▼
PostgreSQL
  │
  ▼
pgvector
```

Each vector must be associated with the original chunk.

The system should never store a vector without a reference to its source chunk.

---

# 36. Embedding Storage

The vector record should maintain:

```text
Chunk ID

Document ID

Topic ID

Embedding
```

The embedding dimension depends on the selected embedding model.

The PostgreSQL vector column must match the selected embedding model's output dimension.

---

# 37. Vector Search

When the user asks a question:

```text
User Query
    │
    ▼
Create Query Embedding
    │
    ▼
pgvector Similarity Search
    │
    ▼
Retrieve Top Relevant Chunks
```

The search should normally apply metadata filters.

At minimum:

```text
document_id = current_document_id
```

For topic-specific operations:

```text
document_id = current_document_id

topic_id = current_topic_id
```

---

# 38. RAG Pipeline

The complete RAG pipeline is:

```text
User Query
    │
    ▼
Query Understanding
    │
    ▼
Query Embedding
    │
    ▼
Vector Search
    │
    ▼
Metadata Filtering
    │
    ▼
Retrieve Relevant Chunks
    │
    ▼
Retrieve Related Images / Figures
    │
    ▼
Retrieve Source Metadata
    │
    ▼
Build Context
    │
    ▼
Send Context to Gemini
    │
    ▼
Generate Grounded Response
    │
    ▼
Return Answer + Sources + Images
```

This is the central RAG workflow of the application.

---

# 39. RAG Document Isolation

The RAG query must always be restricted to the current PDF.

Example:

```text
Current User
    │
    ▼
Current PDF ID = 123
    │
    ▼
Vector Search
    │
    ▼
WHERE document_id = 123
```

For topic-specific queries:

```text
WHERE document_id = 123
AND topic_id = 15
```

This prevents unrelated documents from entering the context.

---

# 40. RAG Context Construction

The context sent to Gemini should contain structured information.

Example:

```text
DOCUMENT:
Aircraft Hydraulic Systems

TOPIC:
Hydraulic Pumps

SOURCE:
Page 42
Section: Hydraulic System

CONTENT:
Hydraulic pumps are used to generate hydraulic pressure...

RELATED FIGURE:
Figure 4.2
Hydraulic System Schematic
```

The AI model should receive only the relevant retrieved context whenever possible.

---

# 41. Gemini Prompt Structure

The AI prompt should be structured.

Conceptually:

```text
SYSTEM INSTRUCTION

You are an AME educational assistant.

You must answer using the provided document context.

Do not invent information that is not supported by the provided context.

If the answer cannot be found in the provided context, clearly state that the information is not available in the current document context.


DOCUMENT CONTEXT

[Retrieved Chunks]


SOURCE INFORMATION

[Page and Section Metadata]


USER REQUEST

[User Question]
```

This reduces hallucination risk.

---

# 42. Hallucination Control

The system should use multiple controls.

## Control 1 - RAG

Provide relevant document context.

## Control 2 - Document Filtering

Restrict retrieval to the current PDF.

## Control 3 - Topic Filtering

Restrict retrieval to the current topic where required.

## Control 4 - Structured Prompts

Tell Gemini to use the provided context.

## Control 5 - Source References

Return page and section information.

## Control 6 - Confidence Handling

If the required information cannot be found, the AI should say so instead of inventing an answer.

---

# 43. Study Mode RAG

Study Mode uses:

```text
Current PDF
     │
     ▼
Current Topic
     │
     ▼
Retrieve Topic Chunks
     │
     ▼
Retrieve Related Figures
     │
     ▼
Build Context
     │
     ▼
Gemini
     │
     ▼
Generate Explanation
```

The explanation should be based on the current topic.

---

# 44. Study Mode Topic Progression

The system should process topics sequentially.

Example:

```text
Topic 1
    │
    ▼
Explain
    │
    ▼
Topic 2
    │
    ▼
Explain
    │
    ▼
Topic 3
    │
    ▼
Explain
```

The system should maintain the user's current topic position.

This allows the user to resume their study session.

---

# 45. Quiz Mode RAG

Quiz Mode uses:

```text
Current PDF
     │
     ▼
Current Topic
     │
     ▼
Retrieve Topic Chunks
     │
     ▼
Build Question Generation Context
     │
     ▼
Gemini
     │
     ▼
Generate Questions
```

Questions must be grounded in the current topic content.

---

# 46. Quiz Question Generation Rules

The AI Service must provide Gemini with:

```text
Document ID

Topic ID

Topic Name

Retrieved Context

Question Count

Question Types

Difficulty Distribution

Minimum Text Questions

Minimum Voice Questions
```

The generated questions should be validated by the backend before being accepted.

---

# 47. AI-Generated Question Validation

Generated questions should pass validation.

Validation should check:

```text
Question Exists

Question Type Is Valid

Difficulty Is Valid

Options Exist Where Required

Correct Answer Exists

Correct Answer Matches Options

Question Is Grounded in Context

Text Question Count >= 3

Voice Question Count >= 3
```

If validation fails:

```text
Generated Questions
       │
       ▼
Validation
       │
       ├── PASS
       │    │
       │    ▼
       │ Save
       │
       └── FAIL
            │
            ▼
        Regenerate
```

The system should not blindly trust AI-generated JSON.

---

# 48. Text Answer Evaluation RAG

Text answers should be evaluated using the relevant topic context.

Flow:

```text
Question
    │
    ▼
Expected Answer / Evaluation Criteria
    │
    ▼
Relevant PDF Chunks
    │
    ▼
Student Answer
    │
    ▼
Gemini
    │
    ▼
Evaluation
```

The AI should evaluate:

- Correctness
- Relevance
- Understanding
- Missing critical concepts

---

# 49. Text Answer Evaluation Output

The AI Service should return structured output.

Example:

```json
{
  "score": 8,
  "max_score": 10,
  "is_correct": true,
  "feedback": "The answer correctly explains the main function of the hydraulic pump.",
  "missing_points": []
}
```

The backend should validate the score range before saving it.

---

# 50. Voice Answer Processing

The voice answer pipeline is:

```text
Audio Recording
      │
      ▼
Store in R2
      │
      ▼
Speech-to-Text
      │
      ▼
Transcript
      │
      ▼
Retrieve Topic Context
      │
      ▼
Gemini Evaluation
      │
      ▼
Score
      │
      ▼
Feedback
```

The transcript should be stored if required for result history and debugging.

---

# 51. Voice Answer Evaluation

Voice evaluation should focus on the content of the student's spoken answer.

The pipeline should evaluate:

```text
Question
+
PDF Context
+
Student Transcript
```

The evaluation should not depend on exact sentence matching.

The system should evaluate whether the student demonstrated understanding of the required concept.

---

# 52. Source Reference Generation

Every AI response that uses PDF content should attempt to return source information.

Example:

```json
{
  "sources": [
    {
      "document_id": 123,
      "page": 42,
      "section": "Hydraulic System",
      "topic": "Hydraulic Pumps"
    }
  ]
}
```

The ReactJS frontend can display:

```text
Source

Page 42

Section: Hydraulic System

Topic: Hydraulic Pumps
```

---

# 53. Figure Retrieval During RAG

If a retrieved chunk is associated with a figure:

```text
Retrieved Chunk
      │
      ▼
Check Image / Figure Metadata
      │
      ▼
Figure Found?
      │
      ├── YES
      │    │
      │    ▼
      │ Retrieve Image ID
      │
      │
      └── NO
           │
           ▼
        Continue
```

The final response can contain:

```text
AI Explanation

Source

Related Figure
```

This is particularly important for AME technical documents containing:

- Aircraft system diagrams
- Component diagrams
- Schematics
- Technical illustrations

---

# 54. Example: "Explain Figure 4.2"

User asks:

```text
Explain Figure 4.2.
```

The system should execute:

```text
User Question
      │
      ▼
Detect Figure Reference
      │
      ▼
Search Current Document
      │
      ▼
Find Figure 4.2
      │
      ├── Figure Image
      ├── Caption
      ├── Page
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
Return Response
```

The frontend should display:

```text
Figure 4.2

[IMAGE]

Explanation:
...

Source:
Page 42
Section:
Hydraulic System
```

The AI should not merely explain the figure from general knowledge if the figure is available in the uploaded PDF.

---

# 55. RAG Response Structure

The AI Service should return structured responses.

Recommended format:

```json
{
  "answer": "The hydraulic system uses...",
  "sources": [
    {
      "page": 42,
      "section": "Hydraulic System"
    }
  ],
  "images": [
    {
      "image_id": "img_42_01"
    }
  ]
}
```

Laravel can then return this data to ReactJS.

---

# 56. AI Service Internal Architecture

The Python AI Service should have the following logical modules:

```text
app/
│
├── api/
│
├── pdf/
│   ├── loader.py
│   ├── detector.py
│   ├── text_extractor.py
│   ├── page_renderer.py
│   ├── image_extractor.py
│   └── table_extractor.py
│
├── ocr/
│   ├── engine.py
│   └── preprocessing.py
│
├── structure/
│   ├── headings.py
│   ├── sections.py
│   └── topics.py
│
├── chunking/
│   └── semantic_chunker.py
│
├── embeddings/
│   └── embedding_service.py
│
├── retrieval/
│   └── vector_search.py
│
├── rag/
│   ├── retriever.py
│   ├── context_builder.py
│   └── source_mapper.py
│
├── study/
│   └── study_generator.py
│
├── quiz/
│   ├── question_generator.py
│   └── question_validator.py
│
├── evaluation/
│   ├── text_evaluator.py
│   └── voice_evaluator.py
│
├── voice/
│   └── speech_to_text.py
│
└── gemini/
    └── client.py
```

---

# 57. Exact End-to-End Processing Workflow

The complete workflow is:

```text
STEP 1
User uploads PDF

        │

STEP 2
ReactJS sends PDF to Laravel

        │

STEP 3
Laravel validates PDF

        │

STEP 4
Laravel stores PDF in R2

        │

STEP 5
Laravel creates PDF record

        │

STEP 6
Laravel dispatches ProcessPdfJob

        │

STEP 7
Queue Worker starts job

        │

STEP 8
Laravel calls Python AI Service

        │

STEP 9
Python retrieves PDF

        │

STEP 10
PyMuPDF loads PDF

        │

STEP 11
Each page is analyzed

        │

STEP 12
Digital / Scanned / Mixed classification

        │

STEP 13
Digital pages use text extraction

        │

STEP 14
Scanned pages use OCR

        │

STEP 15
Rotated pages are normalized

        │

STEP 16
Images and figures are extracted

        │

STEP 17
Tables are extracted

        │

STEP 18
Content is normalized

        │

STEP 19
Document structure is detected

        │

STEP 20
Topics are identified

        │

STEP 21
Content is semantically chunked

        │

STEP 22
Embeddings are generated

        │

STEP 23
Embeddings are stored in pgvector

        │

STEP 24
Source metadata is stored

        │

STEP 25
PDF context is generated

        │

STEP 26
Processing status becomes READY

        │

STEP 27
User opens PDF

        │

STEP 28
User selects Study or Quiz

        │

STEP 29
Relevant topic is selected

        │

STEP 30
RAG retrieves relevant content

        │

STEP 31
Gemini generates response

        │

STEP 32
Sources and images are attached

        │

STEP 33
ReactJS displays result
```

---

# 58. Processing Failure Workflow

If any major processing stage fails:

```text
PDF Processing
      │
      ▼
Error
      │
      ▼
Log Error
      │
      ▼
Update PDF Status
      │
      ▼
FAILED
      │
      ▼
Notify Laravel
      │
      ▼
Frontend Shows Error
```

The system should preserve enough information for developers to identify the failed processing stage.

---

# 59. Retry Workflow

Temporary failures may be retried.

Examples:

```text
Temporary R2 Failure

Temporary AI Service Failure

Temporary Gemini API Failure

Temporary Network Failure
```

The system should use controlled retries.

Conceptually:

```text
Attempt 1
   │
   ▼
Failure
   │
   ▼
Retry
   │
   ▼
Attempt 2
   │
   ▼
Failure
   │
   ▼
Retry
   │
   ▼
Attempt 3
   │
   ▼
Failure
   │
   ▼
Mark Failed
```

Permanent document-processing errors should not be retried indefinitely.

---

# 60. Important Version 1 Rule

The AI must never be allowed to freely answer questions without document grounding in Study Mode or Quiz Mode.

The expected flow is:

```text
PDF Content
    │
    ▼
Retrieve Relevant Context
    │
    ▼
Gemini
    │
    ▼
Response
```

Not:

```text
User Question
    │
    ▼
Gemini
    │
    ▼
Random General Answer
```

The uploaded AME PDF is the primary knowledge source for the student's learning and testing experience.

---

# 61. Version 1 RAG Boundary

For Version 1, RAG is responsible for:

- Finding relevant PDF content
- Providing context to Gemini
- Connecting AI responses to PDF sources
- Supporting topic-specific learning
- Supporting topic-specific quiz generation
- Supporting answer evaluation

RAG is not responsible for:

- User authentication
- User authorization
- File ownership
- Quiz score storage
- Study time tracking
- Dashboard rendering

These responsibilities remain with Laravel and ReactJS.

---

# 62. Final Part 3 Architecture

The final Version 1 AI pipeline is:

```text
                  UPLOADED AME PDF
                         │
                         ▼
                  CLOUDFLARE R2
                         │
                         ▼
                  LARAVEL QUEUE
                         │
                         ▼
                PYTHON + FASTAPI
                         │
                         ▼
                    PyMuPDF
                         │
                         ▼
                PAGE CLASSIFICATION
                         │
          ┌──────────────┼──────────────┐
          │              │              │
          ▼              ▼              ▼
       DIGITAL        SCANNED         MIXED
          │              │              │
          ▼              ▼              ▼
     PyMuPDF Text    Tesseract      Both
          │              │              │
          └──────────────┼──────────────┘
                         │
                         ▼
               TABLE + IMAGE EXTRACTION
                         │
                         ▼
                  SOURCE MAPPING
                         │
                         ▼
                STRUCTURE DETECTION
                         │
                         ▼
                   TOPIC DETECTION
                         │
                         ▼
                  SEMANTIC CHUNKING
                         │
                         ▼
                    EMBEDDINGS
                         │
                         ▼
               POSTGRESQL + PGVECTOR
                         │
                         ▼
                       RAG
                         │
             ┌───────────┴───────────┐
             │                       │
             ▼                       ▼
          STUDY MODE              QUIZ MODE
             │                       │
             ▼                       ▼
       EXPLANATION              QUESTIONS
             │                       │
             ▼                       ▼
         SOURCES                  ANSWERS
             │                       │
             │                ┌──────┴──────┐
             │                │             │
             │                ▼             ▼
             │              TEXT          VOICE
             │                │             │
             │                └──────┬──────┘
             │                       │
             │                       ▼
             │                  EVALUATION
             │                       │
             └───────────────┬───────┘
                             │
                             ▼
                          GEMINI
                             │
                             ▼
                   STRUCTURED RESPONSE
                             │
                             ▼
                          LARAVEL
                             │
                             ▼
                          REACTJS
                             │
                             ▼
                           USER
```

---

# 63. Part 3 Completion Status

Technical Architecture Document Part 3 is complete.

This part defined:

- Exact PDF processing tools
- Exact OCR tools
- Digital PDF processing
- Scanned PDF processing
- Mixed PDF processing
- Rotated page handling
- OCR pipeline
- OCR confidence handling
- Text extraction
- Text normalization
- Header and footer handling
- Table extraction
- Table fallback
- Image extraction
- Figure extraction
- Figure and caption association
- R2 image storage
- Source mapping
- Document structure detection
- Heading detection
- Topic detection
- Topic ordering
- Semantic chunking
- Chunk metadata
- Embedding generation
- pgvector search
- RAG pipeline
- RAG document isolation
- Context construction
- Gemini prompt structure
- Hallucination control
- Study Mode RAG
- Quiz Mode RAG
- AI question validation
- Text answer evaluation
- Voice answer processing
- Voice answer evaluation
- Source reference generation
- Figure retrieval
- AI Service module structure
- Complete PDF processing workflow
- Failure handling
- Retry handling
- Version 1 RAG boundaries

---

# 64. Next Part

The next document should be:

# TAD Part 4 - Database Architecture and Complete ER Diagram

Part 4 will define:

1. Complete database architecture

2. PostgreSQL schema

3. Users table

4. PDFs table

5. PDF processing records

6. PDF pages

7. PDF sections

8. Topics

9. Chunks

10. Embeddings / pgvector

11. Images and figures

12. Study sessions

13. Study progress

14. Quiz sessions

15. Quiz topics

16. Questions

17. Question options

18. User answers

19. Answer evaluations

20. Voice recordings

21. Topic scores

22. Final quiz scores

23. Source references

24. User-PDF relationships

25. Complete ER diagram

26. Primary keys

27. Foreign keys

28. Indexes

29. Relationships

30. Cascade rules

31. Data lifecycle

---

# End of TAD Part 3
