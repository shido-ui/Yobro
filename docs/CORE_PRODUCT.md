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


## Organization
- Multiple PDFs can contribute questions to the same topic.
- Questions from different PDFs may be combined into one shared topic library.
- The organization includes subject sections above chapters/topics.


## Performance & Weak-Area Analysis
- The app tracks detailed student performance.
- Performance data includes accuracy, attempts, mistakes, and topic-wise progress.
- The system uses this data to identify weak areas in detail.
- AI may be used to assist with weak-area analysis.


## Test / Exam Mode
- Tests and exams are generated only from questions extracted from the user's own imported material.
- The app does not introduce outside questions into the user's test library.
- The product follows a BYOM (Bring Your Own Material) model.
- Organized question libraries are the source for test generation.


## Test Customization
- Test generation is highly customizable.
- Users can control test composition down to fine-grained details, including subject/topic, question count, difficulty, and question type.
- Customization applies while remaining restricted to the user's extracted BYOM question library.


## Dashboard
- No dedicated overall study-status/weak-area dashboard is required.


## Search
- The app should provide a comprehensive search system across the user's organized study material.
- Search should cover subjects, chapters, topics, questions, and extracted theory.


## PDF Extraction vs AI Organization
- PDF extraction itself is performed entirely on-device by the integrated MinerU-based extractor; AI is not responsible for extracting the PDF.
- After MinerU extraction, AI helps classify and guide which chapter/topic each extracted question or study item belongs to so the app can automate organization.
- The app remains responsible for storing, organizing, and applying the resulting classification.

## Extracted Content Preservation
- MinerU extraction should preserve relevant diagrams, tables, formulas, and their relationships to the associated question or theory where the source PDF provides those relationships.

## AI Provider / API Support
- Users can configure and use multiple AI providers/API keys.
- The app does not prescribe a specific AI model.
- The user may use whatever model(s) their configured API provider/key supports.
- AI provider choice should remain flexible for classification and analysis.


## Local-First Data & Responsibility
- Student data is stored locally on the user's device.
- The phone's local storage is used for the application's database/data layer.
- No account/login or cloud database is required for the core product.
- When material such as PDFs is sent to an external AI API, the user is responsible for that data transfer and the provider/API they choose.


## Platform & Hardware Target
- Android only for the initial product.
- The app should be highly optimized for Android mid-range devices.
- Low-end phones are not a target.
- Minimum recommended hardware target: 3 GB RAM and a MediaTek Dimensity 6300-class processor.
- The UI is intended to be visually rich/eye-catching, so the target device should be capable of handling the interface smoothly.


## UI / Interaction Direction
- The Android UI should be highly interactive, fluid, and visually engaging.
- Motion should respond smoothly to scrolling and user interaction, with content transitioning/moving into view rather than behaving like a static collection of screens.
- The supplied ORBIS webpage is a reference for the desired interaction philosophy: scroll-driven transitions, animated content entrances, responsive movement, interactive controls, and fluid panels. Its implementation is web-specific and is not itself the Android implementation.
- The Android version should translate the same interaction quality into appropriately optimized native/mobile UI.


## Theme
- Dark mode only.


## PDF Import
- Users import one PDF at a time.
- PDF extraction is performed on-device using the integrated MinerU-based extractor.


## PDF Processing Experience
- After importing a PDF, the app shows a real-time processing/progress view.
- The processing view should communicate MinerU extraction and subsequent organization/analysis progress as it happens.


## Cancelled PDF Processing
- If the user cancels PDF processing, the partially extracted results are discarded and deleted.


## Original PDF Retention and Extraction Quality
- The original imported PDF is deleted after successful extraction and verification.
- Prioritize the highest practical extraction accuracy for all questions and associated content, including equations, diagrams, and tables.
- Verify extraction before deleting the source PDF; flag uncertain or potentially missing content rather than assuming perfect extraction.


## Extraction Verification
- Yobro should automatically verify extracted content for completeness and potential extraction errors.
- The user should also have an opportunity to review the extracted content before the original PDF is permanently deleted.


## Extraction Verification Gate
- If automatic verification identifies a potentially missing or incorrect question, the original PDF must not be deleted until the issue is resolved.
