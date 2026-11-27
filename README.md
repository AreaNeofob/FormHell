# Form Hell

> There is no cancel button.

**Form Hell** is a short, one-page escape game built with HTML and CSS only. You are trapped inside a corporate exit form. Read the policies, answer its increasingly unreasonable questions, and prove that you are eligible to leave.

No JavaScript, external assets, libraries, network requests, or saved data are used.

## Play

Open [index.html](index.html) in a current desktop browser. The game works offline.

Modern versions of Chrome, Edge, Firefox, and Safari are supported. The game relies on the CSS `:has()` selector to reveal each completed stage.

## How it works

The game unfolds down a single page. Each cleared room reveals the next one:

1. Identity Verification
2. Temporal Compliance
3. Emergency Contact
4. Consent to Be Processed
5. Exit Interview

Look closely at the expandable policy notes. They contain everything needed to solve the puzzles.

There is more on the page than the main route:

- **Assistance** is available in some rooms. It is logged.
- **Three policy violations** are hidden in places a careful applicant would not look.
- **A second disposition** exists for applicants whose answers are wrong in exactly the right way.

Both endings show a performance review with your assistance requests, confessed violations and a rating.

The ending includes a print-ready certificate. Use the browser’s normal print command to print it; plain HTML cannot open the print dialog by itself.

## Implementation notes

- Native form validation (`required` and `pattern`) checks text answers.
- Radio buttons and checkboxes hold choice state.
- CSS `:valid`, `:checked`, and `:has()` control the progression, completion messages, and progress rail.
- `<details>` elements provide optional lore and hints.
- CSS counters tally opened hints and confessed violations for the scorecard.
- `@media print` displays whichever ending is unlocked cleanly when printed.

## Files

```text
FormHell/
├── index.html      # The complete playable game, including its CSS
├── build-plan.md   # Design plan and implementation decisions
└── README.md       # This file
```

## Development

`index.html` is intentionally self-contained. Keep it free of JavaScript and external dependencies. If the game design changes, update `build-plan.md` alongside the implementation.
