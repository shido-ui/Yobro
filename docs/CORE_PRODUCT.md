# Yobro — Core Product Definition

## Product
Yobro is a complete offline-first study organization app aimed mainly at students who struggle to organize study material and prepare for tests.

## Connectivity
- The app works offline for its core functionality.
- Internet is used when connected to an external AI API through the user's configured API key.
- AI is not responsible for the app's core organization logic.

## Core Flow
1. User imports a PDF.
2. The app extracts questions and relevant study material.
3. AI classifies which topic/chapter each item belongs to.
4. The app performs the actual categorization/organization locally based on the AI's classification.
5. The student can review and manually correct extracted questions/categories.
6. The student can create their own chapters/topics.
7. The student can move/manipulate extracted questions between topics.
8. The student can delete questions from a topic when an extraction or categorization mistake is made.
9. The student can read extracted theory.
10. The app provides summaries and key formulas using LaTeX.
11. The student can practice extracted questions.
12. Extracted diagrams and tables should be preserved where applicable.
13. The student can use the material for quick revision and tests.

## Question Types
The system should support:
- MCQ
- Numerical questions
- Subjective questions

For MCQs:
- AI supplies the short correct answer (e.g. option/one-word answer).
- The answer is stored with the question.
- The test system uses the stored answer to determine correctness.
- The app does not generate long-form explanations.
- Students can independently search elsewhere for explanations if they want them.

## Manual Control
Users have full manual correction capability over their organized material. They can:
- Create chapters/topics.
- Move or otherwise manipulate extracted questions within their organization.
- Delete questions from topics when extraction or categorization is incorrect.
- Correct AI/app organization rather than being locked into automated categorization.

## Product Principle
Keep the application logic and organization local. AI primarily provides classification/guidance and short answer information where needed; deterministic app logic handles persistence, categorization, testing, and user interaction.

## Documentation Rule
This document records established product decisions only. It must not be used to invent future requirements or roadmap items.

The roadmap and future plans remain controlled by the project owner.
