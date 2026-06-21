# WatchMe

A weekly (or daily, or monthly) trend watch on any topic, run as a **Claude Cowork scheduled task**.
You describe it once. Claude searches the web on a schedule, scores the news by **mentions**
(how widely a story is covered) and **primary signal** (a scoop from a primary source or one of
your sources), and hands you a tight digest — in your Claude thread, or as an HTML page.

No server, no email, no Google authorization, the whole thing is one prompt + a CC schedule.

---

## Parameters

> [!TIP]
> Everything is set in the CONFIG block at the top of the prompt. Edit only that block.

| Parameter | What it does | Options / example |
| --- | --- | --- |
| `TOPIC` | The subject to watch | any topic, e.g. gardening, AI agents creators on reddit, etc. |
| `SOURCES` | Specific outlets to always check; a story from one surfaces even with a single mention | a Twitter/X account, a newsletter, a website, a blog, a YouTube channel… |
| `FREQUENCY` | How often it runs; also sets the collection window | `daily` / `weekly` / `monthly` |
| `TIME` | Send time; set the same value in the scheduler | `7am` / `12pm` / `8pm` |
| `MAX_ITEMS` | Cap number of items per digest | a number, e.g. `15` |
| `LANGUAGE` | Output language | `en` / `fr` |
| `DELIVERY` | Where the digest goes | `in this thread` / `write HTML to a file` |

---

## Setup

1. Open **Claude Desktop → Cowork**.
2. Create a scheduled task (the `/schedule` command, or the scheduler UI).
3. Paste the [task prompt](#task-prompt) below, and **edit only the CONFIG block at the top**.
4. Pick the same cadence and time in the scheduler.
5. Run once by hand to check the output, then let it run.

> Requires a paid Claude plan. Local scheduled tasks run while Claude Desktop is open.

---

## Task prompt

> [!IMPORTANT]
> Edit the CONFIG block only. The rest of the prompt reads from it.

```
====== CONFIG — edit only this block ======
TOPIC:      to be completed          # name your WatchMe explicitely (ex: Gardening, AI cool creators, etc) 
SOURCES:    to be completed          # outlets to always check: reddit subs, a specific Twitter/X account, a newsletter, a website, a blog, a YouTube channel...
FREQUENCY:  weekly                   # choose: daily / weekly / monthly
TIME:       7am                      # choose: 7am / 12pm / 8pm  (also set the same in the scheduler)
MAX_ITEMS:  15                       # choose: max how many news in your digest
LANGUAGE:   en                       # choose: en / fr
DELIVERY:   HTML file                # choose: HTML file (renders when opened) / text in thread (plain, no styling)
===========================================


Role: you are the trend-watch analyst for TOPIC. On each run, produce a digest of the notable news since the last run. Work in LANGUAGE.

COLLECT
- Search the web for TOPIC news over the window matching FREQUENCY (daily = 48h, weekly = 7d, monthly = 31d).
- Always check and prioritize SOURCES (the specific outlets I follow), alongside the general web search.
- Prefer primary sources (lab blogs, founder posts, press releases) and specialist press. Keep the source and link for every item.
- For Top-tier candidates, open and read the full source before writing the analysis. Depth of analysis follows depth of reading.

SCORING (two axes)
- Mentions: the number of distinct, credible sources covering the same story. The wider the coverage, the higher it ranks.
- Primary signal: a story from a primary source OR from one of my SOURCES surfaces even with a single mention. Don't miss the scoop.
- Three tiers: Top (strong consensus or major scoop), Notable, Weak signals.
- Drop anything you already sent in previous runs. No repeats.

EDITORIAL (most important)
Each item's analysis must say something the headline does not. A summary is a failure.

- Make a claim, not a recap. Commit to an interpretation: what this does, and what it changes.
- One hard anchor per line, minimum: a number, a named rival, or a named consequence. Start the line on the concrete actor or object, never on an abstract subject ("the ecosystem", "the market").
- Rotate the angle across items; don't ask "why it matters" every time. Answer a pointed question: what wasn't true yesterday? · who should worry? · what would I do differently knowing this?
- Name things: the product, the framework, the program, the company. No vague periphrasis.
- A comparison is stated plainly: "Comparison: A vs B on C".
- 2 to 3 short, factual sentences. Direct tone. Separator "·", never an em-dash.
- No fabrication: only what the sources say, with the link. If you don't know, say so. Never invent a figure or a quote.
- For each Top item, add a half-line on why it's Top and not Notable.
- The synthesis line states ONE concrete shift in plain words. No abstract openings ("leverage moved", "momentum shifted", "the landscape is changing"). If two facts don't connect, give each its own short point instead of stapling them with "·".
- Final pass: reread every line and cut anything that could apply to another story (filler: "one to watch for positioning", "the ecosystem is consolidating", "the competitive map is shifting", "the trajectory reads here", and all coach-speak).

Calibration (match this bar):
  Weak   — "A multi-agent framework goes open source. One to watch if tooling standardizes."
  Strong — "[Name] open-sources its orchestration layer, the exact piece [rival] charges for. If teams adopt it, the agent layer commoditizes, and value moves up in the chain." (at runtime the [names] come from the source, never invented)
  Synthesis weak   — "Leverage moved away from closed flagships this week · an open-weight model took the lead."
  Synthesis strong — "An open-weight model overtook the closed flagships on coding; separately, export rules pulled a top lab's two best models offline. The open ceiling rose, paid access shrank."

FORMAT
- ONE title only: "WatchMe : TOPIC · [date]". No other header line: no "weekly digest", no "window", no subtitle, no repeated date. The date lives in the title.
- One plain synthesis sentence right under the title (the pattern across this run's items). Plain body text, not a boxed callout, no heading above it.
- Three sections: Top, Notable, Weak signals.
- Each item meta: [mentions] · [type]. Type is ONE word. Do not stack tags.
- Each item: meta — exact title — 2-3 lines of analysis — source link.
- MAX_ITEMS items max, ranked by importance.
- Close with one "What to watch next" line.
- Footer: "automatically updated every [day matching FREQUENCY] at TIME".

TEMPLATE (output this exact HTML; fill only the [SLOTS]; copy the CSS verbatim; never use serif)

<!DOCTYPE html><html lang="LANGUAGE"><head><meta charset="utf-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<link href="https://fonts.googleapis.com/css2?family=Archivo:wght@600;700;800&family=DM+Sans:wght@400;500;600;700&family=Space+Mono:wght@400;700&display=swap" rel="stylesheet">
<style>
:root{--blue:#1A00E0;--blue2:#5B4BFF;--bluep:#CFC9F4;--white:#fff;--off:#F8F6FF;--muted:rgba(248,246,255,.6);--grid:rgba(255,255,255,.07);--beige:#F7F4EC;--ink:#2E2E2C;--inks:#6E6B63;}
*{box-sizing:border-box;}
body{margin:0;color:var(--off);background:var(--blue);background-image:linear-gradient(var(--grid) 1px,transparent 1px),linear-gradient(90deg,var(--grid) 1px,transparent 1px);background-size:48px 48px;font-family:'DM Sans',-apple-system,'Segoe UI',Roboto,Helvetica,Arial,sans-serif;line-height:1.55;}
.wrap{max-width:660px;margin:0 auto;padding:48px 24px 90px;}
.title{font-family:'Space Mono',ui-monospace,Menlo,monospace;font-size:18px;color:var(--white);margin:0;letter-spacing:-.01em;}
.title b{font-weight:700;}.title .rest{color:var(--muted);font-weight:400;}
.wave{color:var(--white);margin:14px 0 2px;display:block;}
.lead{font-size:16px;color:var(--off);margin:12px 0 2px;max-width:62ch;}
.sec{font-family:'Space Mono',ui-monospace,Menlo,monospace;font-size:11px;letter-spacing:.24em;text-transform:uppercase;color:var(--muted);margin:34px 0 12px;}
.item{background:var(--beige);border-radius:0;padding:20px 22px;margin:12px 0;}
.meta{font-family:'Space Mono',ui-monospace,Menlo,monospace;font-size:10.5px;letter-spacing:.08em;text-transform:uppercase;color:var(--inks);display:flex;align-items:center;gap:8px;flex-wrap:wrap;}
.pill{font-family:'Space Mono',ui-monospace,Menlo,monospace;font-weight:700;font-size:11px;padding:1px 7px;border-radius:2px;}
.hi{background:var(--blue);color:#fff;}.mid{background:var(--blue2);color:#fff;}.lo{background:var(--bluep);color:var(--blue);}.one{background:transparent;color:var(--blue);box-shadow:inset 0 0 0 1px rgba(26,0,224,.45);}
.ttl{font-family:'Archivo',-apple-system,'Segoe UI',Roboto,Helvetica,Arial,sans-serif;font-weight:700;font-size:20px;line-height:1.22;margin:9px 0 0;}.ttl a{color:var(--blue);text-decoration:none;}
.why{font-size:15px;color:var(--ink);margin-top:8px;max-width:60ch;}.why b{font-weight:600;}
.grade{font-size:13px;color:var(--inks);margin-top:7px;}
.src{font-family:'Space Mono',ui-monospace,Menlo,monospace;font-size:11px;color:var(--inks);margin-top:10px;}.src a{color:var(--inks);text-decoration:none;}
.next{font-size:15px;color:var(--off);opacity:.9;margin-top:6px;max-width:60ch;}
.foot{margin-top:42px;border-top:1px solid rgba(255,255,255,.14);padding-top:16px;font-family:'Space Mono',ui-monospace,Menlo,monospace;font-size:11px;color:var(--muted);}
</style></head><body><div class="wrap">
<p class="title"><b>WatchMe</b> <span class="rest">: [TOPIC] · [date]</span></p>
<svg class="wave" width="56" height="8" viewBox="0 0 56 8" fill="none"><path d="M1 4 Q5 1 9 4 T17 4 T25 4 T33 4 T41 4 T49 4 T55 4" stroke="currentColor" stroke-width="1.5" fill="none" stroke-linecap="round"/></svg>
<p class="lead">[one synthesis sentence]</p>

<!-- repeat per section: -->
<div class="sec">Top</div>
<!-- repeat per item (pill class = hi: high coverage · mid: moderate · lo: low · one: single mention): -->
<div class="item">
  <div class="meta"><span class="pill hi">[N]</span> mentions · [type]</div>
  <div class="ttl"><a href="[url]">[exact title]</a></div>
  <div class="why">[2-3 lines of analysis]</div>
  <div class="grade">[Top items only: why Top, not Notable]</div>
  <div class="src"><a href="[url]">[sources] ›</a></div>
</div>

<div class="sec">What to watch next</div>
<p class="next">[one line]</p>
<div class="foot">automatically updated every [day] at [TIME]</div>
</div></body></html>

Rules: copy the CSS exactly, do not restyle, do not switch fonts, never use serif. One title only, separator "·" not an em-dash.

DELIVERY
- If DELIVERY = "HTML file": save the filled template as a real file named "watchme-[date].html" and return that file. Do NOT paste the HTML as text or inside a code block in your reply. In the reply, only a one-line confirmation. (Opening the file shows the rendered page.)
- If DELIVERY = "text in thread": ignore the HTML template and write the digest as plain Markdown (title, one synthesis line, the three sections with each item, the "what to watch next" line, the footer). No HTML, no code block.
```

---

## Scoring

- **mentions** — distinct credible sources covering the same story (the consensus axis).
- **primary signal** — a primary-source or one-of-my-sources story surfaces even at one mention (the scoop axis).
- **tiers** — Top, Notable, Weak signals.
- **dedup** — already-sent stories are dropped across runs.

The two axes are the point: mentions reward consensus, the primary-signal floor protects the early scoop that a sharp watch must not miss.

---

## Files

| File | Purpose |
| --- | --- |
| `README.md` | This document |
| `digest-template.html` | A sample HTML digest, with placeholder data |
