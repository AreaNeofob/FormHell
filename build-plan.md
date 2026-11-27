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

Implemented additions:

- **Hidden policy violations (3).** Each sits in an easily overlooked `<details class="violation">` and contains a confession checkbox. Ticking it commits the violation and shows a violation note.
  - Masthead fine print `*Terms apply.` → Clause 0.3 → `#v-clause` ("I confirm that I have not read Clause 0.3.")
  - Room 2 redacted profile line `Termination date: ██████████` → `#v-redacted` ("I did not see that.")
  - Room 3 footnote marker `¹` → `#v-footnote` ("I would have called for help anyway.")
- **Assistance requests (3).** `<details class="hint">` in Rooms 2, 4 and 5 give direct nudges. Opening one shows "— request logged" and counts against the player.
- **Secret ending — Terms Assumed.** In Room 4, checking *all six* statements is philosophically consistent: if you are the terms, you have read, understood and agreed to yourself, and personhood was never needed. Room 4 shows the red stamp `TERMS ASSUMED`. Room 5 stays hidden, the secret ending appears instead, the whole progress rail turns red, and its label becomes `YOU ARE THE COMPLIANCE EVENTS`.
- **Scorecard.** Both endings show an "Applicant performance review": assistance requests logged (n / 3), violations confessed (n / 3), alternative disposition (`Unclaimed` / `Claimed`) and a rating:
  - `Exemplary compliance` — no hints opened and no violations.
  - `Repeat offender` — all three violations confessed.
  - `Adequately processed` — everything else.

## Management Track

A second, harder route through the form. The player picks it on the opening screen. It reuses the room, policy, hint, violation, stamp, feedback, scorecard and ending components, and adds a `<select>`, which the standard track does not use.

### Track selection

Two radios in the masthead (`#mode-standard`, checked by default, and `#mode-management`) choose the track. `body:has(#mode-management:checked)` hides `<main id="compliance">` and its Begin button, and shows `<main id="management">` and its own Begin button. Each track keeps its state when the player switches back and forth.

### M1 — Headcount planning

> Enter your team's current headcount.

The org chart lists: Manages: You (applicant), You (reviewer), Your replacement (pending). Interns: 2, unpaid, therefore not headcount. Contractors: 4, documents, not people. The methodology says headcount counts roles, not people, and that a budgeted role held by nobody still counts. **Answer: 3** (text input, `pattern="3|three"`, case-insensitive).

### M2 — Budget allocation

> Select the quarter in which your departure will be budgeted.

The memo says Q1–Q4 are fully allocated, unbudgeted departures roll into the next available quarter, and Q0 is reserved for things that never happened. **Answer: Q5.** This is a required `<select>`, gated by the `#q5` option being `:checked`, because any non-empty option would make the select `:valid`.

### M3 — Delegation

> Delegate your departure.

The policy says tasks may be delegated only to a direct report, and to the one with the least seniority. **Answer: Your replacement** (still pending). HR and Your manager are upward delegation and get a wrong-answer message.

**Secret ending — Self-management achieved:** choosing *Yourself* is wrong but consistent, since you do report to yourself. Like the standard secret ending, it replaces the rest of the track: M4–M5 stay hidden, the rail turns red and its label becomes `YOU ARE THE MANAGEMENT OBJECTIVES`.

### M4 — Termination effective date

> Enter the effective date of your termination.

The personnel file shows Date of birth: 21 August 1996, Hire date: Before, Last day: Pending. The guideline says terminations take effect on the earliest entry that is actually a date. **Answer: 1996-08-21.** This is a text input whose pattern accepts common formats: `1996-08-21`, `19960821`, `21/08/1996`, `21.8.1996`, `21-08-1996`, `08/21/1996`, `21 August 1996`, `21 Aug 1996` and `August 21, 1996`.

### M5 — Sign-off

> Enter the authorization code for your departure.

The format is `HEADCOUNT-QUARTER-YEAR-DELEGATE`, where YEAR is the effective year and DELEGATE is the last word of the delegate. **Answer: `3-Q5-1996-REPLACEMENT`** (case-insensitive `pattern`).

### Endings

- **Exit approved, with severance:** the amber standard Management ending.
- **Self-management achieved:** the red secret ending.

Both have a scorecard with Management-specific ratings: `Adequately managed`, `Exemplary leadership` and `Repeat offender`.

### Management violations and hints

- **Violations:** the masthead `#v-clause` (shared with the standard track), the M1 intern wellbeing report `#v-interns`, and the M4 disciplinary record `#v-record`.
- **Hints:** in M2, M4 and M5.

## File structure

```text
FormHell/
├── index.html        # Entire game structure and game text
└── styles.css        # Theme, transitions, validation gates, print ending
```

For maximum portability, the final version may instead be a single self-contained `index.html`. It should work offline, use no libraries, store no data, make no network requests, and contain no JavaScript.

## Implementation refinements

The following details were added during feasibility review and are required for the HTML-only implementation:

- Room gates are cumulative: each room, ending and progress dot requires *every* earlier room's solution selector, tested on `form`/`body` with chained `:has()`. (The earlier adjacent-sibling gates only checked the immediately preceding room, so un-solving Room 1 after finishing left Rooms 4–5 and the ending visible.) Flat ID conditions are used because `:has()` cannot be nested inside `:has()`.
- Do not rely on a `<fieldset>` itself becoming `:valid`; CSS instead tests the required descendant input states with `:has(...)`.
- A radio group is not sufficient by itself for Rooms 2 and 3 because any selected required radio is valid. Gate those rooms by the ID of the specific correct radio being `:checked`.
- Gate Room 4 by requiring both correct checkbox IDs to be checked and explicitly excluding every forbidden checkbox ID via `:not(:has(...))`.
- The final room uses five `required` text inputs with exact `pattern` values. The ending appears only when all five individual inputs are `:valid`.
- Completion messages and stamps must use the same exact solution selectors as their room gates. A generic `input:valid` selector is not sufficient because any selected required radio is valid, even when it is the wrong answer.
- Text-answer patterns accept either letter case while CSS presents answers in uppercase, so the puzzle is about the words rather than keyboard capitalization.
- The implementation will be a single `index.html` with embedded CSS so it can be opened offline. `build-plan.md` remains the source of truth for future changes.
- The secret ending is gated by Rooms 1–3 plus all six Room 4 checkbox IDs being `:checked`. It is mutually exclusive with the standard Room 4 gate, which excludes the four extra boxes.
- Scorecard numbers use CSS counters: `counter-reset: hints violations` on `body`, `counter-increment` on `.hint[open]` and on `.violation:has(input:checked)`, displayed with `counter()` in `::before`. The scorecard is later in document order than every counted element, so it shows final totals. Closing a hint un-logs it; this is accepted.
- Ratings are mutually exclusive `:has()` conditions on `body` (none open/checked vs. all three violation IDs checked); the standard rating is the default.
- Print styles hide `.room` elements, not `form`, because both endings live inside the form. Printing shows whichever ending is currently unlocked.
- Each track's ratings are scoped to its own `<main>` (`#compliance …` / `#management …`) and its own violation IDs. The masthead violation `#v-clause` counts for both. Counters need no scoping, because `counter-increment` ignores elements inside the hidden track.
- The standard track's progress rail and secret-rail rules are scoped to `#compliance`, and the Management ones to `#management`.
- A `<select>` gate tests the specific `<option>` with `:checked`; its wrong-answer state is `option:not(#q5):not([value=""]):checked`. Never nest `:has()` inside `:has()`: an invalid selector drops the *whole* selector list it is in.
- **Wrong-answer messages:** each wrong radio in Rooms 2–3 has its own message, gated by that radio's ID. Room 4 shows "over-consent" when any forbidden box is checked, unless all six are (the secret path). Room 5 and M5 show a message while any field is invalid and non-empty, like Room 1.
- **Panel jitter:** only wrong *choices* (radios, the Room 4 checkboxes and the select) shake their panel. Text fields don't, so they don't shake while being typed in. A CSS animation replays only when its name changes, so wrong radios cycle between the identical keyframes `jitter-1`, `jitter-2` and `jitter-3`, and switching from one wrong option to another replays the shake.
- **Red alert after Room 3:** a fixed `body::after` overlay plays `redalert` once whenever Rooms 1–3 become solved, in the standard track only.
- **Progress count:** "n / 5" uses a `resolved` counter that is reset on `.progress` and incremented by the same cumulative rules that light each dot. The count element sits *after* the dots in the markup, because counters follow document order.
- **Loading bar:** the masthead bar animates a registered `@property --pct` integer and its own width to 99% over 7 s, then pulses forever. With reduced motion it jumps straight to 99%.
- **Never give a single answer as a range or step limit.** Number and date inputs let the player step with arrow keys, the scroll wheel or the spinner, and stepping always lands on a value the field accepts. `min="3" max="3"` therefore hands out the answer. Exact-value answers must be text inputs with a `pattern`. (M1 and M4 originally used `type="number"` and `type="date"` and could be solved by pressing an arrow key.)
