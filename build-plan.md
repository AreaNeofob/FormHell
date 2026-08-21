# Form Hell — Build Plan

## Concept

**Form Hell** is a one-page, HTML-and-CSS-only escape-room game. The player wakes inside an endless corporate onboarding form. Each field is a supernatural puzzle: rather than entering ordinary information, the player interprets bureaucratic wording, exploits form rules, and provides the valid answer that unlocks the next section.

Tone: Kafkaesque, funny, and slightly unsettling.

Goal: complete the final **Exit Interview** and escape the form.

## Technical approach

The game uses no JavaScript.

- Each room is a `<fieldset>` containing native form inputs.
- Correct answers use `required`, `pattern`, `min`, `max`, input types, and `<select>` where appropriate.
- CSS uses `:valid` and `:has()` to reveal the next room.
- CSS uses `:invalid` for inline error messages.
- Checkboxes and radio buttons drive choice puzzles through `:checked`.
- `<details>` holds optional hints, lore, and fake policy documents.
- A progress rail changes as rooms are cleared.
- A reset link reloads the page.

Target modern browsers: Chromium, Safari, and Firefox, all of which support the required CSS features.

## Player flow

```text
Arrival
  ↓
1. Identity Verification
  ↓
2. Temporal Compliance
  ↓
3. Emergency Contact
  ↓
4. Consent to Be Processed
  ↓
5. Exit Interview
  ↓
Escape / ending
```

Every room appears only when the preceding room is valid. It is still one document; it unfolds vertically as the player succeeds.

## Visual direction

- Near-black blue-gray background.
- Off-white form panels, with an aged-paper texture made from CSS gradients.
- Red for errors and threats; sickly green for valid states; amber for warnings.
- System sans-serif for interface text, monospace for system messages, and a restrained serif for uncanny narrative passages.
- Small CSS-only effects: blinking cursor, almost-complete loading bar, invalid-panel jitter, and a stamp effect on completion.
- No images required; the game is fully self-contained.

## Opening screen

**Title:** `FORM HELL`

**Subtitle:** `There is no cancel button.`

Copy:

> Welcome, Applicant. Your exit request has been received. Please complete the following mandatory fields to demonstrate eligibility for release.

A `BEGIN COMPLIANCE` anchor scrolls the player to Room 1.

## Room 1 — Identity Verification

**Prompt:**

> Please enter your legal name exactly as it appeared before you were born.

### Puzzle

A nearby policy excerpt says that all employees are registered before acquiring a body, and that initial identity is recorded as `UNASSIGNED`. The required answer is therefore `UNASSIGNED`.

### Form rule

```html
<input
  type="text"
  required
  pattern="UNASSIGNED"
  title="Identity does not match pre-birth records."
>
```

### Feedback and reward

- Invalid: `No living identity found in our records.`
- Valid: `Identity temporarily accepted. Please do not become attached to it.`
- Completion stamp: `EXISTENCE ACKNOWLEDGED`

## Room 2 — Temporal Compliance

**Prompt:**

> State the date you began working here.

### Puzzle

The employee profile lists:

- Date of birth: `21 August 1996`
- Contract commencement: `Immediately`
- Orientation status: `Ongoing`
- Employment duration: `Before`

The player chooses **Before** in a required radio group. Only that option clears the room.

### Feedback and reward

> Thank you. Seniority has been adjusted retroactively.

The progress rail advances to `2 / 5 COMPLIANCE EVENTS RESOLVED`.

## Room 3 — Emergency Contact

**Prompt:**

> In the event of an emergency, whom should we notify?

### Options

- My family
- Local authorities
- Myself
- The emergency

### Puzzle

An expandable policy note says that emergencies are autonomous corporate stakeholders and must be informed of changes to their operational environment. The correct answer is **The emergency**.

### Feedback and reward

> Emergency notified. It appreciates the courtesy.

After completion, the background gains a brief red-alert pulse and settles.

## Room 4 — Consent to Be Processed

This is the game’s main form-state puzzle.

**Prompt:**

> To continue, confirm all statements that are true.

### Checkboxes

- I have read the terms.
- I understand the terms.
- I have agreed to the terms.
- I am the terms.
- I waive my right to be a person.
- I acknowledge that this acknowledgment is non-binding.

### Puzzle

A legal note explains that understanding is not required, agreement cannot be verified, and the company recognizes only documents—not people.

The player must select exactly:

- `I am the terms.`
- `I acknowledge that this acknowledgment is non-binding.`

All other checkboxes must remain clear.

### CSS gate

Use `:has()` to show the next room only when the two correct boxes are checked and no prohibited box is checked.

### Feedback and reward

> Consent has been extracted. Thank you for volunteering it.

A faux-PDF stamp appears: `PERSONHOOD: ARCHIVED`.

## Room 5 — Exit Interview

**Prompt:**

> Complete the following statement to confirm your departure: “I, ________, who began ________, authorize ________ to notify ________ that I am ________.”

### Puzzle

The player combines the answers already given:

- Identity: `UNASSIGNED`
- Began: `BEFORE`
- Authorize: `THE TERMS`
- Notify: `THE EMERGENCY`
- Am: `NON-BINDING`

The completed statement reads:

> I, UNASSIGNED, who began BEFORE, authorize THE TERMS to notify THE EMERGENCY that I am NON-BINDING.

### Form rule

Use five required text fields, each with an exact `pattern` value. The fieldset becomes valid only when all five entries are correct.

### Feedback

> Departure request received. Reviewing your eligibility to have ever been here.

The final escape link appears.

## Ending — Release Granted

The final link targets an ending panel.

**Heading:** `EXIT APPROVED`

Copy:

> Congratulations, Applicant. You have successfully escaped Form Hell.
>
> Your replacement has been created from the information you provided.

Final controls:

- `View print-ready certificate of nonexistence` — a print-friendly view using `@media print`; the player uses the browser’s native print command because HTML alone cannot open the print dialog.
- `Return to Form Hell` — reset/reload link.
- `Close this tab and pretend it was voluntary` — deliberately nonfunctional styled text; HTML cannot close a browser tab.

## Difficulty and replayability

The base game should take 8–15 minutes. All answers are fairly clued in visible material, but close reading is rewarded.

Potential additions:

- Two or three optional hidden policy violations revealed in overlooked `<details>` content.
- A secret ending for a subtly incorrect but philosophically consistent Room 4 choice.
- A scorecard based on hints opened, violations committed, and secret-ending status.
- `Standard Escape` and `Management Track` modes as separate sections sharing the same components and styles.

## File structure

```text
FormHell/
├── index.html        # Entire game structure and game text
└── styles.css        # Theme, transitions, validation gates, print ending
```

For maximum portability, the final version may instead be a single self-contained `index.html`. It should work offline, use no libraries, store no data, make no network requests, and contain no JavaScript.

## Implementation refinements

The following details were added during feasibility review and are required for the HTML-only implementation:

- Use adjacent sibling CSS gates: a solved room reveals only the immediately following room. This prevents later content being visible before its prerequisite is met.
- Do not rely on a `<fieldset>` itself becoming `:valid`; CSS instead tests the required descendant input states with `:has(...)`.
- A radio group is not sufficient by itself for Rooms 2 and 3 because any selected required radio is valid. Gate those rooms by the ID of the specific correct radio being `:checked`.
- Gate Room 4 by requiring both correct checkbox IDs to be checked and explicitly excluding every forbidden checkbox ID via `:not(:has(...))`.
- The final room uses five `required` text inputs with exact `pattern` values. The ending appears only when all five individual inputs are `:valid`.
- Completion messages and stamps must use the same exact solution selectors as their room gates. A generic `input:valid` selector is not sufficient because any selected required radio is valid, even when it is the wrong answer.
- Text-answer patterns accept either letter case while CSS presents answers in uppercase, so the puzzle is about the words rather than keyboard capitalization.
- The implementation will be a single `index.html` with embedded CSS so it can be opened offline. `build-plan.md` remains the source of truth for future changes.
