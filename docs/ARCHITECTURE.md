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
