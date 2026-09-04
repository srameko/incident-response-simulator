# Nightwatch — SOC Triage Simulator

A single-file training simulator for first-line SOC analysts. Ten realistic alerts, one night shift.
The learner types a name, works the queue, and gets scored on the verdict, the response actions taken,
the actions they should *not* have taken, and how long the case took.

No backend, no login, no build step — one `index.html` file.

## Run it on GitHub Pages

1. Create a repository and put `index.html` in the root.
2. **Settings → Pages → Build and deployment → Source: Deploy from a branch**, branch `main`, folder `/ (root)`.
3. Wait ~1 minute. The site is at `https://<user>.github.io/<repo>/`.

Locally: just open `index.html` in a browser, or `python3 -m http.server`.

## What the exercise teaches

| Skill | How it's trained |
|---|---|
| Ownership | A case is locked until the analyst assigns it to themselves |
| Cost of investigation | Each enrichment lookup adds 20 s to handling time, so they learn to pick the decisive one |
| Verdict discipline | Three options: true positive, false positive, **authorised activity** — the distinction most beginners miss |
| Proportionality | Forbidden actions cost points: isolating a laptop during an account takeover, disabling 4,000 accounts after a failed spray, tipping off an insider suspect |
| Sequence | Containment before analysis on ransomware; preserve-then-escalate on insider cases |

Every case ends with a debrief explaining the reasoning, not just the score.

## Gamification

XP and six ranks (Tier 1 Trainee → Incident Commander), verdict streaks, per-case score breakdown,
eight badges, and an end-of-shift report with accuracy and a proportionality percentage.

## Adding your own alerts

Everything lives in the `ALERTS` array in the `<script>` block. Copy one entry and edit:

```js
{
  id:'ALT-4481', sev:'high',          // critical | high | medium | low
  target:140,                          // target handling time in seconds
  title:'…', source:'…', time:'02:15',
  entities:{ Host:'…', User:'…' },     // shown as case metadata
  summary:'…',
  evidence:[ ['02:14:01','log line'] ],
  enrich:[ { id:'e1', label:'…', cost:20, key:true, result:'…' } ],  // key: the decisive lookup
  verdict:'true_positive',             // true_positive | false_positive | benign
  required:['isolate_host'],           // +14 each, −10 if missed
  ok:['add_watchlist'],                // neutral
  forbidden:['tune_rule'],             // −12 each
  debrief:'Why this is the right answer.'
}
```

Action IDs come from the `ACTIONS` object — add new ones there and they appear in the UI automatically.

## Notes

- Light and dark themes; follows the OS setting on first load, toggle in the header, choice remembered.
- Progress is per-browser-session and is not sent anywhere. Refreshing starts a new shift.
- Keyboard accessible, responsive down to phone width, respects `prefers-reduced-motion`.
