# Yobro Architecture

## Database
- SQLite is the main local database for Yobro.

## Android UI
- The Android application UI is built with Kotlin and Jetpack Compose.

## Database Layer
- Jetpack Room is used as the SQLite database access layer.

## Application Architecture
- Yobro follows an MVVM + Repository architecture pattern.

## Application Architecture
- The Android application follows MVVM with a Repository pattern.

## PDF Extraction Runtime
- MinerU runs locally on the Android phone as part of Yobro's on-device PDF processing architecture.
- PDF extraction does not depend on a remote extraction service.

## Background PDF Processing
- Long-running PDF extraction should use Android WorkManager if it can be integrated without materially increasing implementation complexity.
- If WorkManager would substantially complicate the build, Yobro should prefer a simpler reliable on-device processing approach.

## Local Storage
- Yobro stores its database, extracted study data, and application-generated files in Android's internal app storage.
- Core data does not require external/shared storage or a cloud database.

## AI Processing Boundary
- AI API calls begin only after MinerU has completed on-device PDF extraction.
- AI receives extracted content for organization/classification and analysis rather than performing the initial PDF extraction.

## AI Failure & Manual Organization
- AI organization is optional and must not be a dependency for retaining extracted content.
- If an AI API call fails, extracted content remains stored locally.
- Users can manually categorize extracted questions and study content.
- Full editing capabilities remain available regardless of AI availability.

## Automatic AI Organization
- After MinerU extraction completes, AI classification starts automatically without requiring a separate manual start action.
- If AI classification fails, Yobro provides a manual organization tool so the user can categorize the extracted content themselves.
