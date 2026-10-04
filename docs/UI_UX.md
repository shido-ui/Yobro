# Yobro UI/UX Architecture

## Interaction Direction
- The UI is visually appealing and highly interactive.
- Tapping controls should produce responsive visual feedback and purposeful animations.
- Scrolling should drive smooth transitions and animated content movement.
- The experience should feel fluid rather than like a static collection of screens.
- The design is dark mode only.
- Interaction quality should be optimized for the target Android mid-range hardware.

## Primary Navigation
- Navigation should include only the sections necessary for the complete Yobro study workflow.
- The navigation foundation uses a bottom navigation structure with a custom interactive visual treatment.
- Core destinations are:
  - Library — subjects, chapters, topics, extracted theory, and questions.
  - Practice — practice extracted questions.
  - Tests — create and take highly customizable tests from the user's question library.
  - Search — comprehensive search across the study library.
  - Settings — app, AI provider/API configuration, and relevant local controls.
- Navigation transitions should use fluid animations and responsive visual feedback rather than static screen changes.

## Subject & Chapter Browsing
- Subjects open into interactive chapter cards.
- Chapter cards use smooth entrance, tap, and scrolling animations.
- Content should transition fluidly as the user browses rather than appearing as static lists.

## Topic Browsing
- Topics use the same interactive card-based visual language as chapters.
- Opening a topic reveals its questions through smooth transitions.
- Question content should appear through fluid, responsive interactions rather than static screen changes.

## Theory Reading
- Theory is presented in a clean interactive reading canvas.
- Sections, formulas, diagrams, and tables can appear through smooth scroll-driven transitions.

## Question Counts
- Chapter cards display the total number of questions the user has in that chapter.
- Topic cards display the total number of questions the user has in that topic.
- Counts reflect the user's current local question library.

## Question Interaction
- Tapping a question card expands the question in place with a smooth animation.
- The primary question-reading interaction should avoid unnecessary full-screen navigation.

## Question Card Answer Display
- Outside test/exam mode, expanding a question reveals its options and saved answer directly within the card.
- The saved answer is visually separated from the question/options.
- Test/exam mode does not reveal the saved answer during the test.

## Test/Exam Environment
- Tests/exams use a separate dedicated test environment rather than the normal question-card browsing interface.
- The normal question-card expanded view is not used as the test-taking environment.

## Test Question Navigation
- The dedicated test environment presents one question at a time.
- Users navigate between questions with Next and Previous controls.

## Test Question Navigator
- The dedicated test environment includes a question-number navigator.
- The navigator shows question numbers and their answered/unanswered state.

## PDF Processing Screen
- PDF processing has a dedicated screen/workspace.
- The screen presents extraction progress, verification status, and review actions without cluttering the main Library interface.

## Creation and Editing Controls
- Creating and editing subjects, chapters, and topics uses animated bottom sheets rather than separate screens.
- Bottom sheets should provide responsive motion and keep the primary navigation uncluttered.

## Question Editing
- Editing a question uses an animated bottom sheet.
- All question-editing fields are accessible without leaving the current topic.
