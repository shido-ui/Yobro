# Yobro — Final Master Architecture

Status: Canonical architecture and implementation plan
Product: Yobro
Platform: Android only
Primary principle: local-first, BYOM, AI-assisted organization, deterministic local ownership
Source of truth: this document

This document consolidates the previous Yobro architecture, core-product decisions, UI/UX decisions, the broader ModuleIQ architecture plan, and the open-source reuse strategy. If another document conflicts with this file, this file is authoritative unless the product owner explicitly changes the decision.

## 1. Product Definition

Yobro is a single-user, offline-first Android study-organization and practice application.

The user owns the data. There is no account system, login, signup, tenant model, cloud database, or required backend account.

The product is Bring Your Own Material (BYOM):
1. Import the user's study material.
2. Extract it locally.
3. Verify extraction.
4. Organize extracted knowledge.
5. Let the user correct the organization.
6. Build reusable subject/chapter/topic/question libraries.
7. Search and read the resulting knowledge.
8. Practice and test from the user's own extracted questions.
9. Track performance and weak areas locally.
10. Optionally use user-configured AI providers for classification and analysis.

AI is replaceable intelligence. It is never the authoritative database and is never required for the core application to remain useful.

## 2. Non-Negotiable Constraints

- Android only for the initial product.
- Single user only.
- No login/signup/authentication for the product.
- No cloud database.
- No mandatory cloud backend.
- Core functionality must work without internet.
- All primary data is stored in Android app-private internal storage.
- PDF extraction happens on-device.
- The original PDF is retained until extraction and verification requirements pass.
- AI receives extracted/selected content, not the original PDF for extraction.
- User edits are authoritative over AI suggestions.
- Tests contain only questions derived from imported user material.
- Users cannot author completely new questions from scratch.
- Duplicating an existing question is allowed.
- Deletion is permanent after confirmation.
- Dark mode only.
- UI must be highly fluid and interactive but remain performant on mid-range Android hardware.
- Do not build fake placeholder functionality where a real implementation or explicit integration boundary is expected.

## 3. Final System Architecture

Android application
→ Jetpack Compose UI
→ ViewModels / UI state
→ Domain services
→ Repositories
→ Room / SQLite + local file storage
→ Processing orchestration
→ MinerU-based document engine
→ Verification engine
→ Local knowledge engine
→ Local search/indexing
→ Practice/adaptive/analytics engines
→ Optional AI provider layer

The UI must not depend directly on MinerU, AI SDKs, SQLite implementation details, or file-system paths.

### Layer 1 — Presentation
- Jetpack Compose
- Navigation
- Reusable UI components
- Animation/motion system
- ViewModels
- UI state models

### Layer 2 — Application / Domain
- Document ingestion service
- Processing orchestration
- Verification
- Knowledge organization
- Question management
- Search
- Practice
- Tests
- Performance
- Analytics
- Review
- Backup/export/import
- Master Search

### Layer 3 — Repository
Repositories expose stable interfaces to domain/application code.

Examples:
- DocumentRepository
- SubjectRepository
- ChapterRepository
- TopicRepository
- QuestionRepository
- PracticeRepository
- TestRepository
- SearchRepository
- SettingsRepository
- AIProviderRepository
- ProcessingJobRepository
- BackupRepository

### Layer 4 — Persistence
- Room
- SQLite
- SQLite FTS5 where appropriate
- Android app-private files
- Encrypted local secrets for API credentials

### Layer 5 — Processing
- MinerU adapter
- OCR
- layout analysis
- equation extraction
- table extraction
- diagram/figure handling
- normalization
- validation
- deduplication
- chunking/indexing

### Layer 6 — Optional Intelligence
Provider-neutral AI interface supporting:
- classification
- organization
- confidence analysis
- weak-area analysis
- summarization
- short answer extraction where required
- image analysis where required

The AI provider can be changed without changing the local knowledge model.

## 4. Source-of-Truth Data Ownership

The local database is the authoritative state.

The application must never depend on a giant JSON document as its primary database.

Use normalized entities with foreign keys and indexes.

Core entities:
- Subject
- Chapter
- Topic
- Subtopic
- Concept
- Document
- DocumentPage
- DocumentAsset
- TheorySection
- Question
- QuestionOption
- Solution
- Equation
- Table
- Diagram
- Relationship
- ProcessingJob
- ProcessingEvent
- VerificationResult
- ReviewItem
- AIProvider
- AIRequest
- AIResult
- SearchIndex
- PracticeSession
- PracticeAttempt
- Test
- TestQuestion
- TestAttempt
- MasteryRecord
- AnalyticsEvent
- AppSetting
- BackupRecord

The established user-facing hierarchy remains:
Subject → Chapter → Topic → Question

Theory belongs to Topic.

The internal provenance model must allow one question/theory item to retain links to the source document, page, extraction run, and relevant assets.

## 5. Document Ingestion Architecture

The user imports exactly one PDF at a time.

Pipeline:
PDF selected
→ validate file
→ copy/retain source in app-private processing storage
→ create Document record
→ create ProcessingJob
→ process asynchronously
→ report progress
→ persist intermediate results
→ verify
→ review if required
→ organize
→ index
→ mark complete
→ allow original PDF deletion only after the deletion gate passes

### Original PDF deletion gate

The source PDF must not be deleted until:
1. extraction completed;
2. extraction validation completed;
3. potentially missing/incorrect questions are resolved or explicitly accepted;
4. required assets are preserved;
5. processing is marked successful.

If the user cancels:
- partial extracted results are deleted;
- the original source remains safe until the cancellation policy completes.

If extraction fails:
- source material remains available for retry.

## 6. MinerU Integration

MinerU is the primary document-intelligence engine.

Do not blindly copy the entire MinerU application into Yobro.

Create a stable MinerUAdapter boundary.

Responsibilities:
- invoke supported local extraction;
- normalize MinerU output;
- preserve page/source coordinates where available;
- extract text;
- detect structure;
- preserve formulas;
- convert equations to LaTeX representation;
- preserve tables;
- preserve figures/diagrams;
- preserve relationships between extracted assets and surrounding content;
- return structured intermediate data.

The adapter must isolate the rest of Yobro from MinerU implementation changes.

### Android strategy

The complete desktop/GPU MinerU environment must not be assumed to work unchanged on Android.

Use the smallest viable local processing stack and progressively qualify:
1. CPU-compatible extraction;
2. ONNX-compatible components where practical;
3. Android/ARM64-compatible native components;
4. llama.cpp/Vulkan only where it provides a real benefit and is stable;
5. fallback processing paths for unsupported components.

Extraction accuracy is prioritized, but the runtime must remain realistic for the target phone.

## 7. Extraction Normalization

MinerU output is converted into Yobro's canonical intermediate representation.

Normalization must produce:
- document metadata;
- pages;
- headings;
- sections;
- paragraphs;
- questions;
- options;
- candidate answers;
- theory;
- equations;
- tables;
- diagrams;
- figures;
- source locations;
- relationships;
- extraction confidence;
- warnings.

Every extracted object should retain provenance whenever available.

Example:
Question → Document → Page → Source region → Extraction run

This enables review, debugging, duplicate detection, and future reprocessing.

## 8. Extraction Verification

Verification is deterministic wherever possible.

Checks include:
- empty or malformed question text;
- missing question numbers where detectable;
- missing options;
- suspiciously short questions;
- detached answer choices;
- malformed equations;
- missing formula relationships;
- missing tables;
- missing diagrams;
- page-level extraction gaps;
- duplicate extraction;
- inconsistent source references.

Verification produces:
- status;
- confidence;
- warnings;
- errors;
- affected objects;
- affected pages;
- recommended action.

Verification must not silently claim perfect extraction.

## 9. Review System

The Review Center is the human correction layer.

Review items may include:
- uncertain question extraction;
- uncertain classification;
- malformed equations;
- detached diagrams;
- table extraction problems;
- duplicate questions;
- low-confidence AI output;
- missing content warnings.

Actions:
- approve;
- edit;
- reject;
- merge;
- split;
- retry;
- manually organize.

User edits always become authoritative local data.

## 10. AI Provider Architecture

AI is optional and provider-neutral.

Define an AIProvider interface.

Capabilities may include:
- classifyQuestion
- classifyTopic
- classifyChapter
- classifySubject
- analyzeWeakAreas
- summarizeTheory
- extractShortAnswer
- verifyExtraction
- analyzeImage

Provider adapters can support:
- Gemini-compatible APIs;
- OpenAI-compatible APIs;
- other user-selected providers;
- future local models.

The rest of Yobro must not contain provider-specific business logic.

### AI request flow

Local data
→ retrieve minimum required context
→ construct structured request
→ send to configured provider
→ validate structured response
→ store result + provenance
→ apply only approved/accepted changes

Never send an entire library when a small relevant context is sufficient.

### API keys

Keys are local secrets.

They must not be stored as ordinary plaintext database fields.

Use Android Keystore/encrypted storage where practical.

Provider switching must never rewrite or corrupt the knowledge database.

## 11. Automatic Organization

After successful extraction:

Extracted item
→ determine relevant context
→ AI classification
→ confidence score
→ accept high-confidence result
→ retry low-confidence result
→ manual review/fallback if still uncertain
→ local organization

Questions are classified independently to maximize placement accuracy.

Context may include:
- nearby theory;
- section heading;
- page;
- source document;
- surrounding questions;
- extracted topic hints.

AI classification does not directly become database state without validation.

## 12. Knowledge Organization

Users can:
- create subjects;
- rename subjects;
- move subjects;
- merge subjects;
- delete subjects;
- create chapters;
- rename chapters;
- move chapters;
- merge chapters;
- delete chapters;
- create topics;
- rename topics;
- move topics;
- merge topics;
- delete topics.

Deletion requires confirmation.

Cascade deletion is explicit and permanent.

Questions from multiple PDFs can belong to the same topic.

The source document remains provenance, not an organizational boundary.

## 13. Question System

Supported question types:
- MCQ
- Numerical
- Subjective

Question fields should include, where applicable:
- question text;
- type;
- options;
- stored answer;
- source;
- subject;
- chapter;
- topic;
- difficulty;
- confidence;
- provenance;
- extracted assets;
- performance data.

Users can edit extracted questions.

Users cannot create completely new questions from nothing.

Users can duplicate an extracted question.

A duplicate remains material-derived and starts in the same chapter/topic placement as its source.

Permanent deletion requires confirmation.

## 14. Theory System

Theory is owned by Topic.

Theory can contain:
- text;
- headings;
- equations;
- diagrams;
- tables;
- source references;
- notes.

Theory is editable after extraction.

Equation storage uses a canonical LaTeX representation.

KaTeX is used for rendering where appropriate.

## 15. Asset System

Diagrams, figures and tables are first-class local assets.

Each asset should support:
- source document;
- source page;
- owning object;
- asset type;
- local path;
- dimensions;
- extraction metadata;
- relationships.

Do not flatten all assets into one text blob.

## 16. Search Architecture

Yobro has two distinct search systems.

### Study Search

Searches:
- subjects;
- chapters;
- topics;
- questions;
- theory;
- concepts;
- equations where practical;
- source documents where useful.

Use local indexes.

Recommended strategy:
- SQLite FTS5 for exact/full-text search;
- normalized metadata filters;
- optional local semantic/vector index when justified by device resources.

Search must work offline.

### Master Search

Master Search searches the application's own interface.

It is available from the main screen only.

It opens as a full-screen search overlay.

It searches:
- Settings;
- Library;
- PDF Import;
- Processing;
- Review;
- Practice;
- Tests;
- storage controls;
- AI provider controls;
- other navigable interface actions.

It supports keywords and synonyms.

Example:
PDF
→ Import PDF
→ Processing
→ Extraction
→ Verification

Selecting a result closes the overlay and navigates directly to the destination/control.

No recent-search history is shown.

## 17. Practice Engine

Practice uses only extracted user questions.

Modes:
- Fast
- Deep
- Boss
- Custom

Core flow:
select source scope
→ build local question set
→ run one-question-at-a-time practice
→ record attempt
→ immediately show correctness
→ record mistake/confidence/time
→ update local performance
→ generate session summary

Practice supports:
- subject scope;
- chapter scope;
- topic scope;
- question count;
- difficulty;
- question type;
- weak-area targeting.

## 18. Test / Exam Engine

Tests are separate from normal question browsing.

Tests use only the user's organized question library.

Customization can include:
- subject;
- chapter;
- topic;
- number of questions;
- difficulty;
- question type;
- other available local composition constraints.

Test environment:
- one question at a time;
- Next;
- Previous;
- question-number navigator;
- answered/unanswered state;
- no correctness feedback during test.

After submission:
- score;
- accuracy;
- mistakes;
- topic-wise performance;
- detailed mistake review.

Mistakes can expand to show:
- question;
- user's answer;
- correct answer.

## 19. Adaptive Learning / Mastery

Performance data is local.

Track:
- attempts;
- correct/incorrect;
- response time;
- skips;
- confidence;
- hints if applicable;
- solution views if applicable;
- topic;
- difficulty;
- error category;
- session;
- test/practice origin.

Build local mastery records per topic/concept.

Adaptive selection should use deterministic local rules first.

AI may assist with interpretation, but must not be required for basic weak-area detection.

## 20. Analytics

Analytics remain local.

Useful metrics:
- total questions;
- attempted questions;
- accuracy;
- mistakes;
- topic accuracy;
- chapter accuracy;
- subject accuracy;
- test performance;
- practice performance;
- weak topics;
- improvement over time.

No cloud telemetry is required.

The product does not need a dedicated overall dashboard if that conflicts with the established Yobro product definition. Analytics should instead be accessible where useful through Practice/Tests/Settings/search flows.

## 21. Processing Job Architecture

All long-running work must be represented as persistent jobs.

Job states:
- Pending
- Running
- WaitingForReview
- Retrying
- Completed
- Cancelled
- Failed

Jobs must store:
- type;
- document;
- current stage;
- progress;
- timestamps;
- retry count;
- error;
- resumability information.

The processing engine must support safe interruption and restart.

Heavy work must never block Compose rendering.

WorkManager may be used if it integrates cleanly. Otherwise use the simplest reliable Android-native background mechanism that satisfies persistence and recovery.

## 22. Failure & Recovery

The system must survive:
- app restart;
- service restart if a local service is used;
- Android process death;
- temporary storage failure;
- malformed PDFs;
- OCR failure;
- extraction failure;
- AI timeout;
- network loss;
- provider quota/error;
- user cancellation.

Rules:
- AI failure never destroys extracted content.
- Network failure never destroys local content.
- Cancellation deletes partial extraction but protects the source.
- Extraction failure leaves the source available for retry.
- Verification failure blocks unsafe source deletion.
- Completed processing stages should not be repeated unnecessarily.

## 23. Backup / Export / Import

Local data must be exportable.

Backup should include:
- database;
- document metadata;
- study structure;
- questions;
- theory;
- assets;
- settings that are safe to export;
- provenance.

Secrets/API keys should not be exported as plaintext.

Restore must validate:
- schema version;
- database integrity;
- asset references;
- migration compatibility.

Import/export must not require a cloud service.

## 24. Storage Architecture

Use Android app-private internal storage.

Logical areas:
- database/
- documents/
- assets/
- processing/
- search/
- backups/
- cache/
- logs/

Storage cleanup must distinguish:
- source PDFs;
- extracted assets;
- database state;
- temporary processing data;
- search indexes;
- cache.

User-visible storage controls must show what will be removed.

## 25. UI Architecture

Jetpack Compose remains the UI foundation.

Primary navigation:
- Library
- Practice
- Tests
- Search
- Settings

Processing/review experiences can be entered from Library, Master Search, or relevant workflows without bloating primary navigation.

### Library

Subject cards
→ Chapter cards
→ Topic window
→ Theory + Questions

Question counts are calculated from local data.

### Topic window

- sticky topic header;
- topic name;
- question count;
- Theory above Questions;
- Theory and Questions are sections, not tabs;
- floating Practice button;
- smooth scroll behavior.

### Question cards

- expand in place;
- show options and saved answer outside test mode;
- test mode uses a dedicated environment.

### Creation/editing

Use animated bottom sheets for:
- subjects;
- chapters;
- topics;
- questions.

Deletion uses confirmation dialogs showing affected counts.

## 26. Motion System

The UI must feel like one coherent interactive system.

Motion applies to:
- cards;
- scrolling;
- navigation;
- bottom sheets;
- question expansion;
- Master Search;
- practice;
- test navigation;
- processing progress;
- result transitions.

Animations should respond continuously to user interaction where appropriate.

Avoid excessive animation that causes jank on mid-range hardware.

Performance has priority over decorative effects.

## 27. Open-Source Reuse Strategy

Yobro should accelerate development by integrating mature open-source components instead of recreating every subsystem.

Primary candidates:
- MinerU — document extraction/intelligence;
- LocalMind — local knowledge/RAG patterns;
- Multimodal Knowledge Engine — PDF knowledge pipeline/citations/RAG patterns;
- Solvor Tutor — practice, confidence, mistakes, local SQLite patterns;
- ExamForge — question/test composition patterns;
- StudyCoach — study UX/spaced-review patterns;
- Paperless-ngx — document management/OCR/reference architecture;
- Khoj — local knowledge/retrieval reference;
- AnythingLLM — workspace/RAG/provider reference;
- PDF.js — PDF rendering/reference where useful;
- KaTeX — equation rendering;
- FastAPI — local service/reference architecture where a service boundary is retained;
- Huey — worker/job architecture reference.

### Reuse rule

For each dependency/repository:
1. inspect license;
2. inspect architecture;
3. identify exact reusable component;
4. determine Android compatibility;
5. prefer a maintained dependency when appropriate;
6. adapt code only where license permits;
7. avoid copying unrelated product logic;
8. preserve Yobro's local-first data model;
9. add attribution/license notices where required;
10. test the integrated component on the target device.

Do not merge repositories wholesale.

## 28. Backend-First Development Strategy

Development proceeds backend/domain-first, then UI.

Order:
1. domain model;
2. Room schema;
3. migrations;
4. repositories;
5. processing job state;
6. document ingestion;
7. MinerU adapter;
8. normalization;
9. verification;
10. review;
11. organization;
12. search;
13. practice;
14. tests;
15. analytics/mastery;
16. AI provider abstraction;
17. backup/restore;
18. Android lifecycle/recovery;
19. UI wiring;
20. motion/performance polish.

This prevents the frontend from becoming a prototype disconnected from real data.

## 29. Master Implementation Phases

### Phase 0 — Source-Code Forensics & Architecture Lock
Audit selected open-source components and record:
- license;
- runtime requirements;
- Android compatibility;
- reusable modules;
- conflicts;
- integration boundaries.

### Phase 1 — Repository Foundation
Establish Android project structure, Gradle configuration, Compose foundation, dependency management, test infrastructure and documentation.

### Phase 2 — Database & Domain Model
Implement Room entities, relationships, migrations, indexes and repository interfaces.

### Phase 3 — UI Shell
Implement dark theme, navigation shell, design tokens, components and motion foundation.

### Phase 4 — File Ingestion
Implement PDF import, app-private storage, validation, document records and processing jobs.

### Phase 5 — MinerU Integration
Integrate MinerU behind an adapter and qualify the smallest Android-compatible execution path.

### Phase 6 — Document Normalization
Convert MinerU output into canonical Yobro entities and provenance.

### Phase 7 — Verification Engine
Implement deterministic extraction checks and deletion gate.

### Phase 8 — Review Center
Implement approve/edit/reject/merge/split/retry/manual organization.

### Phase 9 — AI Provider Engine
Implement provider abstraction, local key storage, provider testing, structured responses and capability detection.

### Phase 10 — AI Organization
Implement subject/chapter/topic classification, confidence, retry and manual fallback.

### Phase 11 — Knowledge Workspace
Implement subject/chapter/topic management, theory, equations, tables, diagrams, provenance and editing.

### Phase 12 — Question Intelligence
Implement question types, options, answers, editing, duplication, deletion and deduplication.

### Phase 13 — Persistent Knowledge Workspace
Ensure all library screens use real Room data and survive process/app restarts.

### Phase 14 — Search Engine
Implement SQLite FTS5 and metadata filtering, followed by optional semantic search if device performance justifies it.

### Phase 15 — Practice Engine
Implement Fast/Deep/Boss/Custom practice and session persistence.

### Phase 16 — Test Engine
Implement customizable tests, test navigator, submission and results.

### Phase 17 — Adaptive Learning
Implement mastery, weak-area detection and adaptive selection.

### Phase 18 — Review & Analytics
Implement mistake review, performance analytics and local progress analysis.

### Phase 19 — Data Portability
Implement export/import/backup/restore with migrations.

### Phase 20 — Provider Switching
Verify that provider changes never alter local knowledge or product state.

### Phase 21 — Security & Data Isolation
Harden API-key storage, local file permissions, local-service exposure if applicable, backups and sensitive-data handling.

### Phase 22 — Android Engineering
Harden lifecycle handling, background processing, WorkManager/native execution, process death recovery and Android storage behavior.

### Phase 23 — Performance Engineering
Optimize large documents, database queries, Compose recomposition, lazy lists, image loading, memory, indexing and processing throughput.

### Phase 24 — Failure & Recovery
Perform deliberate interruption, malformed-file, storage, AI/network and restart tests.

### Phase 25 — Full E2E Validation
Test:
PDF → extraction → verification → review → AI organization → library → search → practice → test → analytics → backup → restore.

### Phase 26 — Regression
Build a regression suite covering all critical flows.

### Phase 27 — Production Hardening
Remove debug-only behavior, improve error messages, validate storage cleanup, tighten permissions and verify release builds.

### Phase 28 — Release Engineering
Create the final Android package, signing/release process, installation flow and device validation.

### Phase 29 — Final Acceptance
The final app must:
- open without login;
- work offline;
- import PDFs;
- process them locally;
- preserve source data until verification;
- build structured knowledge;
- allow manual correction;
- search locally;
- practice;
- create tests;
- track performance;
- optionally connect to AI;
- switch providers;
- export;
- backup;
- restore;
- survive interruption;
- remain useful when AI is unavailable.

## 30. Testing Strategy

### Unit
- database operations;
- organization rules;
- classification validation;
- question selection;
- scoring;
- mastery;
- search;
- backup/restore.

### Integration
- MinerU adapter;
- normalization;
- verification;
- AI provider adapters;
- repository/database interactions;
- asset storage.

### UI
- navigation;
- bottom sheets;
- question expansion;
- Master Search;
- practice;
- tests;
- deletion confirmations.

### Device
- cold start;
- offline start;
- low-memory behavior;
- large PDF;
- long extraction;
- process death;
- configuration changes where applicable;
- storage pressure;
- background/foreground transitions.

## 31. Definition of Done

A feature is not complete merely because its screen exists.

A feature is complete when:
- domain logic exists;
- persistent state exists;
- errors are handled;
- loading/progress states exist;
- restart behavior is safe;
- offline behavior works where required;
- UI uses real data;
- tests exist;
- integration boundaries are real;
- no fake placeholder is presented as production functionality.

The final Yobro product is a real local knowledge and study system, not a PDF viewer with an AI chatbot attached.

## 32. AI Coding-Agent Rules

Any AI coding agent working on Yobro must follow these rules:

1. Read this file before modifying architecture.
2. Do not introduce login/signup/account systems.
3. Do not introduce a cloud database.
4. Do not move core data ownership into an external AI provider.
5. Do not replace Room/SQLite with an opaque JSON database.
6. Do not send original PDFs to AI for extraction.
7. Do not delete original PDFs before the verification gate.
8. Do not invent outside questions for tests.
9. Do not bypass user edits with later automatic AI overwrites.
10. Do not make provider-specific code part of the domain layer.
11. Do not block Compose rendering with extraction/AI work.
12. Do not create fake API responses merely to make UI appear complete.
13. Preserve provenance for extracted knowledge.
14. Preserve recoverability across interruption.
15. Keep heavy processing modular so it can be replaced or optimized later.
16. Prefer reuse of mature open-source components after license and Android-compatibility review.
17. Keep the product single-user and local-first.
18. If a requested change conflicts with this architecture, identify the conflict instead of silently changing the architecture.
19. Product behavior changes must be reflected in docs/CORE_PRODUCT.md.
20. Architecture changes must be reflected in docs/ARCHITECTURE.md.
21. UI behavior changes must be reflected in docs/UI_UX.md.
22. This document is the canonical implementation map.

## 33. Documentation Hierarchy

- docs/ARCHITECTURE.md — canonical technical architecture and implementation plan.
- docs/CORE_PRODUCT.md — established user-visible product decisions.
- docs/UI_UX.md — established interaction and visual behavior.

Implementation order:
Core Product → Architecture → UI/UX → Code

The documents must remain synchronized.

## 34. Final Architecture Summary

Yobro is a local Android knowledge system built around:

Compose
→ ViewModels
→ Domain/Application Services
→ Repositories
→ Room/SQLite + app-private storage
→ Persistent Processing Jobs
→ MinerU Adapter
→ Verification
→ Knowledge Model
→ Local Search
→ Practice/Test/Mastery/Analytics
→ Optional Provider-Neutral AI

The user owns the material, the local database owns the truth, MinerU owns document extraction, deterministic application logic owns organization/persistence/testing, and AI provides optional intelligence.

This separation is intentional and must remain intact as implementation grows.
