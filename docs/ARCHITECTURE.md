# Yobro Architecture

## 1. Platform & Application Structure
- Yobro is an Android-only application.
- The UI is built with Kotlin and Jetpack Compose.
- The application follows MVVM with a Repository pattern.
- Core application state and persistent data are local to the device.

## 2. Local Data Layer
- SQLite is the main local database.
- Jetpack Room is the SQLite database access layer.
- Android internal app storage is used for the database, extracted study data, and application-generated files.
- Core data does not require external/shared storage or a cloud database.

### Core domain hierarchy
- Subject
  - Chapter
    - Topic
      - Question
      - Theory/study content

The data model must also support questions originating from imported material, multiple PDFs contributing questions to the same topic, editable questions/theory, question duplication, permanent deletion, manual organization, and performance data.

## 3. PDF Processing Pipeline
**Import PDF → MinerU extraction → extraction validation → user review → AI organization → local persistence/organization**

- MinerU is responsible for PDF content extraction.
- PDF extraction does not depend on an AI provider or remote extraction service.
- MinerU should preserve relevant text, questions, theory, diagrams, tables, formulas, and their relationships where the source provides them.
- PDF import is one file at a time.
- The original PDF is retained during extraction and verification.
- If processing is cancelled, partial extraction results are discarded and deleted.
- The original PDF is deleted only after successful extraction and verification requirements are satisfied.
- If verification identifies potentially missing or incorrect questions, PDF deletion is blocked until the issue is resolved.
- Users can re-run extraction if dissatisfied with the result.
- Extraction prioritizes the highest achievable accuracy and completeness.

## 4. Processing Runtime
- MinerU runs locally on the Android phone.
- Long-running extraction should use Android WorkManager if it can be integrated without materially increasing implementation complexity.
- If WorkManager would substantially complicate the build, use a simpler reliable on-device processing approach.
- The UI receives processing state and presents real-time progress.
- Heavy extraction work must remain separated from Compose rendering.

## 5. Verification Layer
After MinerU finishes, Yobro validates the extracted representation before allowing the original PDF to be deleted.

Validation should identify potential missing questions, malformed question text, missing options, formula/equation extraction problems, missing or detached diagrams/tables, and other detectable extraction inconsistencies.

The verification result is stored as local processing state and controls whether PDF deletion may proceed.

## 6. AI Processing Boundary
- AI API calls begin only after MinerU has completed on-device PDF extraction.
- The original PDF is not sent to the AI provider for extraction.
- AI receives extracted content for organization/classification and analysis.
- AI provider/model selection remains user-configurable.

### Automatic organization
- AI classification starts automatically after extraction.
- Questions are classified individually for maximum classification accuracy.
- Classification receives relevant extracted context around each question, including nearby theory, section headings, and page context where available.
- Each question is classified independently for subject, chapter, and topic placement.
- Classification results include a confidence score.

### Low-confidence handling
- Low-confidence classifications are automatically sent for a second AI classification attempt.
- If AI organization fails, extracted content remains stored locally.
- Users receive a manual organization tool.
- Full editing capabilities remain available regardless of AI availability.

## 7. Repository & Processing Flow
General application flow:

**Compose UI → ViewModel → Repository → Room / local storage**

PDF processing flow:

**Compose UI → Processing ViewModel → PDF Processing Repository → MinerU → Verification → Room → AI Organization Service → Room → Library UI**

Processing states include:
- Pending
- Extracting
- Verification
- Awaiting review
- Organizing
- Completed
- Cancelled
- Failed / manual fallback

These are internal processing states and do not automatically imply additional product features.

## 8. Failure & Recovery
- AI failure never destroys successfully extracted local content.
- Extraction cancellation removes partial extraction data.
- Verification failure prevents premature source-PDF deletion.
- AI classification failure falls back to manual organization.
- User edits are authoritative over AI-generated organization.
- Safe incremental persistence should be used so interruptions do not unnecessarily repeat completed work.

## 9. Performance Principles
- Heavy PDF processing must not block Compose rendering.
- Database access remains asynchronous.
- Large extracted content should not be unnecessarily duplicated in memory.
- Processing favors controlled, sequential work on target mid-range hardware.
- Stability and extraction accuracy take priority over unnecessary parallelism.

## 10. Security & Data Ownership
- Student data remains on the phone except for content explicitly sent by the user to a configured AI API.
- API credentials must not be stored as plain text in the normal application database.
- AI-provider communication is isolated from the local extraction pipeline.
- No cloud database or account system is required for core operation.

## 11. Architecture Boundary
- This document defines how confirmed Yobro requirements are implemented.
- It must not silently introduce new user-visible product behavior.
- Product decisions that change user-visible behavior belong in `docs/CORE_PRODUCT.md` first; corresponding architecture changes are then reflected here.
