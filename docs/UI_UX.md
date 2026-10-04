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

## Destructive Action Confirmation
- Deleting questions, topics, chapters, or subjects uses a confirmation dialog with a clear warning.

## Deletion Detail
- Deletion confirmation dialogs show the exact content counts that will be permanently deleted, such as the number of questions and topics.

## Library Subject Navigation
- Subject cards are large interactive cards with question counts and subtle animated effects.
- Opening a subject uses an animated transition from the subject card into its chapter view rather than a static page change.

## Library Chapter Navigation
- Chapter cards use the same animated transition pattern as subject cards.
- Opening a chapter transitions smoothly into its topic view rather than using a static page change.

## Topic Content Navigation
- Tapping a topic card opens a dedicated topic window.
- The topic window contains both the topic's theory and all of its questions.
- The topic card does not animate directly into an inline question list.

## Topic Window Sections
- Theory and Questions are kept as separate sections within the dedicated topic window.
- They are not implemented as tabs.

## Topic Window Order
- Theory appears above Questions by default in the dedicated topic window.

## Topic Window Header
- The topic window uses a sticky header showing the topic name while scrolling through theory and questions.

## Topic Header Information
- The sticky topic header displays the topic name and its current question count.

## Topic Practice Action
- The topic window includes a floating Practice button.
- The Practice button starts practice using questions from the current topic.

## Topic Practice Button Behavior
- The floating Practice button is allowed to leave the visible viewport when the user scrolls.
- It does not need to remain persistently visible while scrolling.

## Topic Practice Button Reappearance
- When scrolling back toward the top, the floating Practice button smoothly reappears.

## Topic Practice Button Motion
- Practice button reappearance uses a smooth fade/slide animation rather than an instant appearance.

## Practice Question Navigation
- Practice uses a focused one-question-at-a-time interaction similar to the test environment.
- Users can scroll through upcoming questions before solving or attempting them.

## Practice Question Navigator
- Practice includes a question-number navigator.
- The navigator shows which questions have been attempted.

## Answer Feedback
- Practice shows instant correctness feedback immediately after an answer is submitted.
- Tests do not reveal correctness during the test; correctness/results are shown only after the full test is completed.

## Practice Feedback Visuals
- Correct answers use green feedback with a subtle animation.
- Incorrect answers use red feedback with a subtle animation.

## Practice Session Summary
- After a Practice session, the app shows a session summary.
- The summary includes accuracy, attempted questions, and mistakes.
