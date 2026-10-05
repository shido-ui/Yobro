# Yobro — Open-Source Reuse Ledger

Status: Initial audit ledger
Purpose: Prevent AI coding agents from forgetting what external projects are allowed to contribute to Yobro.

This ledger is subordinate to `docs/ARCHITECTURE.md`. It records the supplied repository audit and must be updated whenever an external component is actually integrated.

## Decision legend

- INTEGRATE: intended implementation dependency/component, subject to Android and license qualification.
- ADAPT: reuse selected code/patterns only after file-level and license review.
- REFERENCE: study architecture/UX/algorithms; do not copy wholesale.
- REJECT: conflicts with Yobro requirements or is not useful enough.

## Audited repositories

| Project | Purpose in Yobro | Decision | Current boundary | Android status |
|---|---|---|---|---|
| MinerU | PDF/document intelligence | INTEGRATE | MinerUAdapter / DocumentEngine | Qualification required |
| PDF.js | PDF viewing/reference | INTEGRATE/ADAPT | PdfViewer | Web/Android integration required |
| KaTeX | LaTeX rendering | INTEGRATE/ADAPT | EquationRenderer | Web/Android integration required |
| Paperless-ngx | Document lifecycle/OCR/search patterns | REFERENCE/ADAPT | Document layer | Server-oriented; not direct |
| Khoj | Local knowledge/RAG patterns | REFERENCE/ADAPT | Search/Knowledge | Not direct |
| AnythingLLM | Workspace/RAG/provider patterns | REFERENCE | AI/Search architecture | Not direct |
| LocalMind | Local document/RAG/provider patterns | REFERENCE/ADAPT | Processing/Search/AI | Desktop-oriented |
| Multimodal Knowledge Engine | Page-aware knowledge/RAG/citations | REFERENCE/ADAPT | Normalization/Search/Provenance | Backend/web-oriented |
| Solvor Tutor | Practice/test/error-review mechanics | REFERENCE/ADAPT | Practice/Test/Mastery | Flutter-oriented |
| ExamForge | Question-generation/composition ideas | REFERENCE | Question/Test | Free-generation conflicts with Yobro |
| StudyCoach | Study UX/spaced-review ideas | REFERENCE | UX/Review | Supplied archive is too small for foundation |

## Important audit findings

### MinerU
Primary extraction engine candidate. Yobro must isolate it behind `DocumentEngine` and `MinerUAdapter`. Full desktop/GPU assumptions are not accepted without Android/ARM64 qualification.

### PDF.js
Mature PDF rendering/reference component. It must remain behind `PdfViewer`; WebView/JavaScript details cannot leak into domain or persistence.

### KaTeX
Math rendering candidate. Canonical Yobro equation data remains LaTeX. KaTeX must remain behind `EquationRenderer`.

### LocalMind / Khoj / Multimodal Knowledge Engine
Useful for local retrieval, hybrid search, provenance, citations and RAG patterns. Their databases/runtime assumptions must not replace Room/SQLite as Yobro's source of truth.

### Solvor Tutor
Useful patterns include confidence tagging, autosave/resume, practice/test mechanics and error-review concepts. Account/cloud architecture is not part of Yobro.

### Paperless-ngx
Useful document lifecycle, OCR, tagging and search architecture. Do not import its server/web product architecture.

### AnythingLLM
Useful workspace/RAG/provider concepts. Too broad to merge and not an Android foundation.

### ExamForge
Useful question-composition ideas, but Yobro's strict material-derived-only question rule overrides free-generation behavior.

### StudyCoach
Useful UX concepts, but the supplied archive is too small to serve as a substantial code foundation.

## License / legal gate

The audit identified license information as a required integration gate, not an assumption. Before copying or packaging external code, record the exact repository version/commit and verify its license plus bundled asset/font licenses.

Known from the supplied audit:
- KaTeX package metadata identified MIT licensing.
- MinerU uses its stated MinerU Open Source License based on Apache 2.0 with additional conditions.

For every other component, the exact license must be verified against the selected version before code is copied or distributed.

## Mandatory integration record

When a component moves from REFERENCE/ADAPT to INTEGRATE, add:
- exact repository URL;
- exact commit/tag/version;
- exact files/modules used;
- license;
- dependencies;
- Android/ARM64 qualification;
- memory/performance measurements;
- Yobro interface;
- fallback implementation;
- tests;
- attribution/notices.

Never copy an entire external repository merely to obtain one feature.
