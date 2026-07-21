# 01_Software_Requirement_Specification.md

## Part 3 - Study Mode and Quiz Mode

**Project Name:** AME AI Tutor

**Document ID:** SRS-01-Part-3

**Version:** 1.1

**Status:** Draft

---

# 1. Study and Quiz System Overview

After a PDF has been successfully processed, the user can choose between:

1. Study from PDF
2. Test My Skills

The application will use the uploaded PDF as the primary source of learning and quiz content.

The AI should not generate study explanations or quiz questions from unrelated information.

The learning process should be topic-based.

The system should retrieve only the relevant content required for the current topic instead of sending the entire PDF to the AI for every request.

---

# 2. PDF Context Introduction

## FR-036 - Show PDF Context

Before starting Study Mode or Quiz Mode, the system should show a short overview of the uploaded PDF.

The context screen should show:

- PDF Name
- Short Description
- Main Topics
- Number of Topics
- Number of Pages
- Estimated Study Time

Example:

Aircraft Electrical Systems

This PDF covers the basic principles of aircraft electrical systems and their maintenance.

Topics:

1. Electrical Fundamentals
2. Batteries
3. Generators
4. Alternators
5. Distribution Systems
6. Circuit Protection

Estimated Study Time: 2 Hours

The user can then select:

- Study from PDF
- Test My Skills

---

# 3. Topic Structure

## FR-037 - Topic-Based Learning

The system should divide the processed PDF into logical topics.

Each topic should contain:

- Topic Name
- Topic Order
- Topic Summary
- Related Text Chunks
- Related Images
- Related Tables
- Page References
- Section References, where available

Example:

PDF

- Topic 1
  - Text
  - Images
  - Tables
  - Page References

- Topic 2
  - Text
  - Images
  - Tables
  - Page References

- Topic 3
  - Text
  - Images
  - Tables
  - Page References

---

# 4. Topic Order

## FR-038 - Maintain Topic Order

Topics should normally be presented in the same logical order as the PDF.

Example:

Chapter 1

↓

Chapter 2

↓

Chapter 3

If the PDF structure is unclear, the system can use AI-based topic detection to create a logical order.

---

# 5. Study Mode

## FR-039 - Start Study Mode

When the user selects "Study from PDF", the system should start a Study Mode session.

The system should identify:

- Current PDF
- Current Topic
- Completed Topics
- Topic Progress
- Overall PDF Progress

---

# 6. Study Mode Flow

The basic Study Mode flow should be:

Start Study Mode

↓

Show PDF Context

↓

Select First Topic

↓

Explain Topic

↓

Show Related Content

↓

Student Interacts

↓

Answer Student Questions

↓

Topic Summary

↓

Mark Topic Completed

↓

Move to Next Topic

↓

Repeat

↓

All Topics Completed

↓

Study Completion

---

# 7. Topic Explanation

## FR-040 - Explain Topic

The AI should explain the current topic using the content extracted from the uploaded PDF.

The explanation should be:

- Easy to understand
- Structured
- Based on the PDF
- Suitable for AME students

The AI should avoid unnecessarily adding information that is not supported by the PDF.

---

# 8. Study Explanation Structure

The AI should normally explain a topic using a structure similar to:

1. Topic Name
2. Introduction
3. Basic Concept
4. Detailed Explanation
5. Important Points
6. Technical Details
7. Examples
8. Practical or Maintenance Relevance
9. Summary

The exact structure may change depending on the topic.

The AI should not force the same explanation format on every topic.

---

# 9. Technical Terms

## FR-041 - Explain Technical Terms

If the PDF contains technical terms, the AI should explain them in simple language.

Example:

Technical Term:

Bus Bar

Simple Explanation:

A bus bar is a conductor used to distribute electrical power to different circuits.

---

# 10. Images and Figures in Study Mode

## FR-042 - Display Related Images

If a topic contains an image or figure, the system should display the relevant image when explaining that topic.

Example:

Figure 4.2

[Original PDF Figure]

Explanation:

This figure shows...

The AI should be able to refer to figures using their original labels when available.

Examples:

- Figure 4.1
- Figure 4.2
- Diagram 3
- Table 5

---

# 11. Image Context

The AI system should know which topic and page an image belongs to.

Example:

Topic:

Aircraft Hydraulic System

Page:

45

Figure:

Figure 4.2

Image:

Extracted Image

This relationship should be stored in the system.

---

# 12. Source References

## FR-043 - Show Source Information

When the AI explains content, the system should provide a source reference whenever the source information is available.

The source should show:

- PDF Name
- Page Number
- Section or Topic
- Figure Number, if applicable
- Table Number, if applicable

Example:

Source

PDF: Aircraft Electrical Systems

Page: 45

Section: Generator System

Figure: Figure 4.2

The user should be able to identify where the information came from.

---

# 13. Study Chat

## FR-044 - Ask Questions During Study

The user should be able to ask questions about the current topic.

Example:

User:

Why is this component required?

AI:

The AI should answer using the current topic context and relevant PDF content.

The AI should prioritize the current topic.

If the answer cannot be found in the uploaded PDF, the AI should clearly state that the required information could not be found in the uploaded PDF.

---

# 14. Topic Completion

## FR-045 - Complete Topic

A topic should be marked as completed when the student finishes the required learning flow for that topic.

The system should save:

- Topic ID
- Completion Status
- Completion Time
- Study Duration

---

# 15. Study Progress

The system should track:

- Current Topic
- Completed Topics
- Total Topics
- Topic Completion Percentage
- Overall PDF Completion Percentage
- Total Study Time

Example:

Study Progress

Completed Topics: 4 / 10

Progress: 40%

Study Time: 45 Minutes

---

# 16. Resume Study

## FR-046 - Resume Study

If the user leaves the application before completing the PDF, the system should remember the user's progress.

When the user returns, the system should show:

Continue Learning

You completed 4 of 10 topics.

Continue from:

Topic 5 - Generator Systems

[ Continue ]

---

# 17. Study Completion

## FR-047 - Complete Study Mode

When all topics are completed, the system should mark Study Mode as completed.

The system should show:

- Total Topics Completed
- Total Study Time
- Completion Percentage
- PDF Completion Status

Example:

Study Completed

Topics Completed: 10 / 10

Completion: 100%

Total Study Time: 2 Hours 15 Minutes

---

# 18. Quiz Mode

## FR-048 - Start Quiz Mode

When the user selects "Test My Skills", the system should start a Quiz Mode session.

The quiz should be conducted topic by topic.

---

# 19. Quiz Flow

The basic Quiz Mode flow should be:

Start Quiz

↓

Show PDF Context

↓

Select Topic

↓

Generate Questions

↓

Ask Question

↓

Receive Answer

↓

Evaluate Answer

↓

Save Result

↓

Next Question

↓

Topic Completed

↓

Calculate Topic Score

↓

Move to Next Topic

↓

Repeat

↓

All Topics Completed

↓

Calculate Final Score

↓

Show Final Report

---

# 20. Quiz Question Types

## FR-049 - Single Choice MCQ

The system should support questions with one correct answer.

Example:

Which component generates electrical power?

A. Battery

B. Generator

C. Transformer

D. Relay

The user can select only one answer.

---

## FR-050 - Multiple Choice Question

The system should support questions with multiple correct answers.

The user should be clearly informed that multiple answers can be selected.

Example:

Select all correct answers.

Which components are part of the electrical system?

- Battery
- Generator
- Bus Bar
- Hydraulic Pump

---

## FR-051 - Text Answer Question

The system should allow users to type an answer.

The answer may be:

- A word
- A sentence
- Multiple sentences

The AI should evaluate the answer based on the relevant PDF content.

---

## FR-052 - Voice Answer Question

The system should allow the user to answer a question using voice.

Basic flow:

AI Question

↓

User Clicks Record

↓

User Speaks

↓

User Stops Recording

↓

Speech-to-Text

↓

AI Evaluation

↓

Score

The system should show the transcribed answer to the user where technically possible.

---

# 21. Quiz Question Generation

## FR-053 - Generate Topic Questions

The AI should generate questions from the current topic.

Questions can be based on:

- Important Concepts
- Definitions
- Technical Details
- Figures
- Tables
- Important Facts
- Understanding of the Topic

The AI should avoid generating questions from unrelated topics.

---

# 22. Number of Questions Per Topic

## FR-054 - Adaptive Question Count

There will be no fixed number of questions for every topic.

The AI should generate enough questions to properly test the important content and concepts of that particular topic.

The system should avoid generating unnecessary or excessive questions.

The number of questions should depend on:

- Topic Size
- Topic Complexity
- Number of Important Concepts
- Amount of Important Information
- Learning Importance of the Topic

Example:

A small topic may require fewer questions.

A large and technically complex topic may require more questions.

The goal is to cover the topic properly without making the quiz unnecessarily long.

---

# 23. Question Type Distribution

## FR-055 - Flexible Question Distribution

There will be no fixed ratio between:

- Single Choice MCQ
- Multiple Choice
- Text Answer
- Voice Answer

The AI can choose the most suitable question types for the current topic.

However, each completed topic quiz must contain at least:

- 3 Text Answer Questions
- 3 Voice Answer Questions

The remaining questions can be distributed between:

- Single Choice MCQs
- Multiple Choice Questions
- Additional Text Questions
- Additional Voice Questions

The exact distribution should depend on the topic.

---

# 24. Topic-by-Topic Quiz

## FR-056 - Complete Current Topic Before Next Topic

The system should not generate questions from the entire PDF at once.

The process should be:

Topic 1

↓

Generate Questions

↓

Answer Questions

↓

Evaluate Answers

↓

Calculate Topic Score

↓

Topic 2

↓

Generate Questions

↓

Answer Questions

↓

Evaluate Answers

↓

Calculate Topic Score

The process continues until all topics are completed.

---

# 25. Question Difficulty

## FR-057 - Difficulty Distribution

Questions should support three difficulty levels:

- Easy
- Medium
- Hard

Easy and Medium questions should be more common than Hard questions.

For a quiz containing 10 questions, the default distribution should be:

- 3 Easy
- 4 Medium
- 3 Hard

The system should adjust the number of questions according to the topic, but should maintain the principle that Easy and Medium questions should not be less than the Hard questions overall.

---

# 26. Quiz Answer Evaluation

## FR-058 - Evaluate Single Choice MCQ

Single-choice questions should be evaluated automatically.

The result should be:

- Correct
- Incorrect

No negative marking will be applied.

---

## FR-059 - Evaluate Multiple Choice Questions

Multiple-choice questions should be evaluated automatically.

The system should determine whether the user's selected answers match the expected correct answers.

For Version 1:

- Fully correct answer receives full marks.
- Incorrect or incomplete selection receives zero marks.
- No negative marking will be applied.

---

## FR-060 - Evaluate Text Answer

The AI should compare the student's answer with the expected concept from the PDF.

Evaluation should focus on meaning and understanding rather than exact wording.

Example:

Expected Concept:

A generator converts mechanical energy into electrical energy.

Student Answer:

A generator produces electrical power from mechanical movement.

The answer may be considered correct because the meaning is equivalent.

---

## FR-061 - Evaluate Voice Answer

Voice answers should follow:

Voice Recording

↓

Speech-to-Text

↓

Transcribed Answer

↓

AI Evaluation

↓

Score

The system should evaluate the transcribed answer.

The evaluation should be based on the meaning of the answer.

Accent or speaking style should not affect the score.

---

# 27. Question Skipping

## FR-062 - Skip Question

The user may skip a question.

A skipped question will be treated as:

Unattempted

The user will receive:

0 marks

for the skipped question.

The skipped question will not receive negative marks.

Example:

Question Marks: 1

Answer: Skipped

Score: 0 / 1

---

# 28. Quiz Submission

## FR-063 - Submit Quiz

The user can attempt the quiz until they click the final Submit button.

Before final submission:

- The user can answer questions.
- The user can change answers where allowed.
- The user can skip questions.
- Skipped questions remain unattempted.

After clicking Submit:

- The quiz attempt is finalized.
- The answers are evaluated.
- The final score is calculated.
- The attempt becomes a completed attempt.

The user cannot modify that submitted attempt.

---

# 29. Quiz Attempts

## FR-064 - Attempt Rules

The user can continue attempting the quiz as long as the quiz has not been submitted.

Once the user clicks Submit:

- The current attempt is completed.
- The result is saved.
- The attempt cannot be modified.

The number of new quiz attempts after submission is not restricted by this requirement.

---

# 30. Quiz Feedback

## FR-065 - Provide Feedback

After evaluation, the system should show:

- Correct / Incorrect
- Score
- Correct Answer or Expected Concept
- Short Explanation

Example:

Result

Correct

Score: 1 / 1

Explanation:

The generator converts mechanical energy into electrical energy.

For an incorrect answer:

Result

Incorrect

Correct Answer:

Generator

Explanation:

A generator produces electrical power from mechanical energy.

---

# 31. Topic Score

## FR-066 - Calculate Topic Score

After completing all questions for a topic, the system should calculate the topic score.

Example:

Topic: Generator Systems

Questions: 10

Correct: 8

Score: 80%

No negative marking will be applied.

---

# 32. Quiz Progress

The system should display:

- Current Topic
- Current Question
- Total Questions
- Completed Questions
- Topic Progress
- Overall Quiz Progress

Example:

Topic 3 of 10

Question 4 of 10

Topic Progress: 40%

Overall Progress: 24%

---

# 33. Resume Quiz

## FR-067 - Resume Quiz

If the user leaves an incomplete quiz, the system should save the current progress where technically possible.

When the user returns, the system should show:

Quiz in Progress

You completed:

Topic 1

Topic 2

Continue from:

Topic 3

The system should allow the user to continue the unfinished quiz.

---

# 34. Passing Score

## FR-068 - Quiz Passing Score

The passing score for the final quiz will be:

75%

The result should be displayed as:

- Passed
- Not Passed

Example:

Score: 82%

Status: Passed

Or:

Score: 68%

Status: Not Passed

The passing score applies to the final overall quiz score.

---

# 35. Final Quiz Score

## FR-069 - Calculate Final Score

After all topics have been completed and the final quiz has been submitted, the system should calculate the overall score.

Example:

Final Score

82%

Topics Completed

10 / 10

The final percentage should be calculated from the total marks obtained divided by the total available marks.

No negative marking will be applied.

Skipped questions receive zero marks.

---

# 36. Final Quiz Report

## FR-070 - Generate Final Quiz Report

The final report should display:

- Overall Score
- Pass / Not Passed Status
- Total Questions
- Correct Answers
- Incorrect Answers
- Unattempted Questions
- Topic-wise Scores
- Strong Topics
- Weak Topics
- Quiz Completion Percentage

Example:

Final Quiz Report

Overall Score: 82%

Status: Passed

Strong Topics:

1. Batteries - 95%
2. Generators - 90%

Needs Improvement:

1. Circuit Protection - 55%
2. Distribution Systems - 60%

---

# 37. Study and Quiz History

The system should store each completed session.

Study Session should contain:

- PDF
- Topic
- Start Time
- End Time
- Duration
- Completion Status

Quiz Session should contain:

- PDF
- Topic
- Questions
- Correct Answers
- Incorrect Answers
- Unattempted Questions
- Score
- Start Time
- End Time
- Submission Time
- Completion Status

---

# 38. Progress Rules

The system should maintain separate progress for:

- Study Mode
- Quiz Mode

Completing Study Mode should not automatically complete Quiz Mode.

Completing Quiz Mode should not automatically complete Study Mode.

Example:

Study Progress: 100%

Quiz Progress: 40%

---

# 39. Session Rules

A user can have:

- One active Study Session per PDF.
- One active Quiz Session per PDF.

The system should save progress when the user:

- Leaves the page.
- Closes the browser.
- Loses internet connection, where technically possible.

---

# 40. AI Content Rules

The AI should prioritize the uploaded PDF as the primary source.

The AI should not intentionally invent information.

If the required information cannot be found in the available PDF context, the AI should clearly state that the information could not be found in the uploaded PDF.

The system should not present unsupported information as if it came from the PDF.

---

# 41. AI Quiz Quality Rules

Generated questions should:

- Be related to the current topic.
- Be understandable.
- Have a clear answer.
- Avoid ambiguous wording.
- Avoid duplicate questions in the same quiz.
- Use information from the PDF.
- Respect the original technical meaning.
- Match the selected difficulty level.

---

# 42. Important Business Rules

## BR-006

Study Mode and Quiz Mode are independent.

---

## BR-007

The AI should process topics in logical order.

---

## BR-008

Quiz questions should be generated topic by topic.

---

## BR-009

The system should complete the current topic's required learning or quiz flow before moving to the next topic.

---

## BR-010

Every quiz answer must be evaluated and stored after submission or evaluation.

---

## BR-011

Text and voice answers should be evaluated based on meaning, not exact wording.

---

## BR-012

Voice answers must be converted to text before AI evaluation.

---

## BR-013

The system should preserve topic, page, section, figure, image, and table relationships wherever available.

---

## BR-014

Study progress and Quiz progress must be tracked separately.

---

## BR-015

The final quiz passing score is 75%.

---

## BR-016

The quiz has no negative marking.

---

## BR-017

Skipped questions are treated as unattempted and receive zero marks.

---

## BR-018

The user can modify answers until the final Submit button is clicked.

---

## BR-019

After final submission, the quiz attempt cannot be modified.

---

## BR-020

Each completed topic quiz must contain at least 3 Text Answer Questions and 3 Voice Answer Questions.

---

## BR-021

The quiz should generate enough questions to properly cover the current topic without generating unnecessary questions.

---

## BR-022

For a 10-question quiz, the default difficulty distribution is:

- 3 Easy
- 4 Medium
- 3 Hard

Easy and Medium questions should generally be more common than Hard questions.

---

# 43. Acceptance Criteria

## Study Mode

Study Mode is complete when:

- User can select Study Mode.
- PDF context is displayed.
- Topics are shown in logical order.
- AI explains topics one by one.
- Related images are displayed.
- Sources are displayed.
- User can ask questions.
- Progress is saved.
- User can resume the session.
- All topics can be completed.
- Study completion is recorded.
- Study time is recorded.

---

## Quiz Mode

Quiz Mode is complete when:

- User can select Quiz Mode.
- PDF context is displayed.
- Topics are processed one by one.
- Questions are generated according to topic content.
- Question count is appropriate for the topic.
- Single Choice MCQs work.
- Multiple Choice Questions work.
- Text questions work.
- Voice questions work.
- At least 3 Text Questions are included per topic quiz.
- At least 3 Voice Questions are included per topic quiz.
- Questions have appropriate difficulty levels.
- Easy and Medium questions are generally more common than Hard questions.
- Answers can be changed before final submission.
- Questions can be skipped.
- Skipped questions receive zero marks.
- No negative marking is applied.
- Answers are evaluated.
- Topic scores are calculated.
- Progress is saved.
- Quiz can be resumed.
- User can attempt the quiz until final submission.
- Final submission locks the attempt.
- Final score is calculated.
- 75% is the passing score.
- Final report is displayed.

---

# 44. Locked Decisions

| Decision ID | Decision                                                                                                                           |
| ----------- | ---------------------------------------------------------------------------------------------------------------------------------- |
| PD-009      | Question count is adaptive. Generate enough questions to cover the topic properly without unnecessary questions.                   |
| PD-010      | No fixed question-type ratio. Every topic quiz must contain at least 3 Text Questions and 3 Voice Questions.                       |
| PD-011      | Passing Score = 75%                                                                                                                |
| PD-012      | No Negative Marking                                                                                                                |
| PD-013      | User can attempt and modify the quiz until clicking Submit. After submission, the attempt is finalized.                            |
| PD-014      | Easy and Medium questions should generally be more common than Hard questions. Default for 10 questions: 3 Easy, 4 Medium, 3 Hard. |
| PD-015      | Skipped questions are treated as Unattempted and receive 0 marks.                                                                  |

---

End of SRS Part 3
