# TakeMeter

> ### 👋 Start here
>
> **New to this repo? Read [RUNNING.md](RUNNING.md) first** — setup, the
> notebook, the baseline, and what to do when something breaks.
>
> Once `python test.py` passes:
>
> ```bash
> head -5 data/practice_labels.csv     # the shape your labels.csv needs
> ```
>
> Then open `takemeter.ipynb` **in this folder** — in VS Code, or with
> `jupyter notebook` if you prefer. Pick the kernel: the `.venv` inside this
> project. Run section 1, which reports the hardware you'll be training on.
> Everything else waits until you have data.
>
> Nothing to upload, nothing to connect, no accounts and no keys. The notebook
> runs on your machine and writes next to your code.
>
> **The rest of this file is your submission.** Fill it in as you go.

---

<!-- ─────────────────────────────────────────────────────────────────────────
     HOW TO USE THIS FILE

     Unit 5 asks for the first five sections. Unit 6 adds the five below them.

     Everything is pasted as TEXT. No screenshots, no images.

     ⚠️ The confusion matrix especially. The notebook prints one as a markdown
     table, ready to copy. A screenshot of a matrix earns nothing. Paste the
     table.
     ───────────────────────────────────────────────────────────────────────── -->

<!-- ═══════════════════════ UNIT 5 — THE BUILD ═══════════════════════ -->

## What This Does

<!-- Your community, and what your classifier sorts posts into. Three or four
     sentences. -->

The community is r/soccer, filtered to posts flaired "News". TakeMeter sorts
the comments people write under those news posts into `analysis`, `hot_take`,
and `reaction`, depending on whether the comment backs its point with a
checkable fact, asserts a claim with nothing behind it, or just expresses a
feeling. Headline-only posts, pure-report posts, and bot/mod comments are
excluded because they contain no opinion to classify.



---

## Label Taxonomy

<!-- Each label: a one-sentence definition and two real examples from your
     reading. Then your decision rule for the hardest boundary.

     The decision rule is worth a point on its own and it's the thing most
     people leave out. Every taxonomy has a hardest boundary. Name yours. -->

### `analysis`

**Definition:** Backs its point with at least one specific checkable fact (a result, standing, stat, date, quote, or named source).

**Example 1:**
> They are mid table with a very thin squad that needed more investment in the summer. Brighton have battered multiple teams this season including Arsenal and Villa. A lot of Brighton's goals were wonder strikes and individual errors. I have no clue why you would consider firing him.

**Example 2:**
> Honestly it's easy to make fun of united but asking any PL side to get 5 wins in a row is a lot isn't it. This article from when Man U had 4 in a row, and played a relegation contender, says that 11 teams had done it since United last had, and Chelsea had in 2026. [OneFootball link, then a dated list: Chelsea – January 2026, Aston Villa – December 2025, Manchester City – December 2025 … Tottenham – October 2024]

### `hot_take`

**Definition:** A confident claim or judgment with nothing checkable offered to back it.

**Example 1:**
> Football fans are generally idiots. Throw abuse at players for 90 minutes, then get offended if a player makes a gesture back.

**Example 2:**
> Don't know how football fans can boo an ex player for every touch and then expect them to be the pinnacle of decorum in return

### `reaction`

**Definition:** An immediate response to the news that expresses a feeling or a quip, with no claim to argue.

**Example 1:**
> Dominance

**Example 2:**
> And that's just at the under 12's games

### The hardest boundary

**Which two labels:** `analysis` and `hot_take`. The post that sits between them is a comment explaining why Azerbaijani fans support any Azerbaijani club in Europe ("It seems you're not familiar with the specifics of football fan culture in the Caucasus…"). It is a reasoned argument, but it rests on local knowledge, not a checkable fact.

**The decision rule I used every time:** If the comment states at least one specific checkable fact in support of its point, it's `analysis`, even if the tone is opinionated. A bare superlative ("different planet", "the gap is wild") or an explanation from general knowledge with no checkable fact is not a fact, so it's `hot_take`.
<!-- e.g. "If the post names a specific checkable fact, it's `analysis`, even
     if the tone is heated." -->



---

## The Dataset

<!-- Where you collected from, how you labelled, your counts, and three hard
     cases. -->

**Where the posts came from:** r/soccer, comments (not posts) copied by hand from seven threads flaired News, Stats or Media: PSG's 13-match unbeaten run, Cubarsí's World Cup passing stats, Man Utd's running stats, the Shearer/BBC segment on Chelsea's distance covered, Messi's walking pace in a Clásico (a 2017 thread, so older comments), the Barcelona/PSG kit clash, and the Clásico cameras. I skipped headline-plus-link posts, original posts, bot and mod comments, comments that only dump stats with no point, and duplicates. Paragraph breaks inside a comment were joined with spaces, usernames were not collected, and links were removed from two comments (noted in the note column).

**How I labelled them:** I labelled 20 comments cold, with no AI, from my definitions alone (rows marked `cold` in the note column). After comparing them with Claude's reading of the same 20 I revised 3 of them (marked `cold; revised`), so 17 are pure cold labels. The other 180 rows were pre-labelled by Claude from my written definitions and decision rule and are marked `prelabeled`; the 34 it flagged as least sure were the ones I went through individually, and I kept all 34 labels, so they are marked `prelabeled; reviewed`. The 20 cold labels are the honest consistency check; the pre-labelled rows are the ones to distrust most if the model turns out to learn something odd.

**Counts per label:**

| Label | Count | Share |
|---|---|---|
| analysis | 63 | 31.5% |
| hot_take | 69 | 34.5% |
| reaction | 68 | 34.0% |
| **Total** | 200 | 100% |

**Three hard cases**

<!-- Any post that made you pause: what it was, which two labels it could have
     been, and what you chose. These are worth more than the easy 190. -->

**1.**
> *The post:* "Bayern are worse than last year"
>
> *Could have been:* `analysis` or `hot_take`
>
> *I chose, because:* `hot_take`. It compares two seasons but offers no checkable fact, so it is a bare claim. My first cold label was `analysis`, which applied my own rule loosely; I revised it, and it is marked `cold; revised`.

**2.**
> *The post:* "Their last lost was against my Sporting! 💚🤍🦁"
>
> *Could have been:* `analysis` or `reaction`
>
> *I chose, because:* `reaction`. It contains a checkable fact (they lost to Sporting) but the fact is not backing any point; it is the commenter celebrating their club. The neighbouring comment "Sporting, obviously. We were the last team to defeat them in the Champions League" is `analysis`, because there the same fact directly answers the question being asked.

**3.**
> *The post:* The long Liverpool comment explaining that "when you reduce the training intensity, you get a few months of improved fitness… but you pay for it later…"
>
> *Could have been:* `analysis` or `hot_take`
>
> *I chose, because:* `hot_take`. It is a careful, reasoned explanation, but nothing in it can be checked: no number, date, result or source. This is the same boundary as my hardest-boundary post, and my rule treats a well-argued comment with no checkable fact as `hot_take`.

---

## The Training Run

<!-- Your starting model, your settings, and anything you changed and why. -->

**Base model:** `distilbert-base-uncased`, fine-tuned for 3-way classification. Trained on an Apple Silicon GPU (`mps`), torch 2.14.1, Python 3.13.5.

**Settings:** 3 epochs, learning rate 2e-5, batch size 16, max length 128 tokens, seed 42.

**Anything I changed from the defaults, and why:** Nothing. All settings are the notebook defaults. One thing to know for next unit: max length 128 means the model only reads the first 128 tokens of each comment, and my longest `analysis` comments can be cut off before their facts appear.

**Split sizes:** train 139 / val 31 / test 30 (stratified 70/15/15 of 200 rows).

| Label | Train | Val | Test |
|---|---|---|---|
| analysis | 43 | 10 | 10 |
| hot_take | 48 | 11 | 10 |
| reaction | 48 | 10 | 10 |

No label has fewer than 8 examples in the test split, but 10 per label is still small: one wrong prediction moves a label's recall by 10 points, so expect scores to move between seeds next unit.



---

## How I Used AI

<!-- Two specific moments — what you asked, what came back, what you changed.

     ⚠️ Plus disclosure of any pre-labelling. If you had a model pre-label a
     batch and then read and corrected every one, say that. It's an allowed
     workflow and disclosing it costs you nothing. Not disclosing it is the
     problem. -->

**Moment 1**

- *What I asked for:* Which community to use. r/soccer was my pick, but the posts I found were almost all goal and highlight clips with no text.
- *What came back:* Claude pointed out that a text classifier can't learn from video-link posts and suggested collecting the comments under News and Stats posts instead, skipping headline-only posts.
- *What I changed:* I switched to collecting comments, and that also made the labels (analysis, hot_take, reaction) fit what people actually write there.

**Moment 2**

- *What I asked for:* Help filling in my acceptance criteria template with accurate facts, because I was short on time.
- *What came back:* Claude read the notebook and the staff taxonomy and drafted all five targets with reasons. It also caught three things in my template: criteria must hold in every seed rather than on average, the confidence measure the notebook reports is by thirds rather than a threshold, and the staff set is labelled under the staff's definitions, not mine.
- *What I changed:* I adopted the reworded criteria. To be plain about it: the five target numbers in `criteria.md` were drafted by Claude, and I committed them as drafted.

**Pre-labelling disclosure:** Claude pre-labelled 180 of my 200 rows (everything except the 20 cold rows) from my written definitions and decision rule, and also helped extract the comments from pasted thread text into CSV. Those rows are marked `prelabeled` in the note column, and the 34 Claude flagged as unsure are marked `prelabeled; reviewed` (I went through each one and kept its label). The 20 cold rows were labelled by me alone.

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy |  |  |  |
| Macro F1 |  |  |  |
| F1 — `label_one` |  |  |  |
| F1 — `label_two` |  |  |  |

**What I predicted before I looked:**
<!-- Milestone 1 asks you to write this BEFORE seeing the trained numbers. A
     prediction made afterwards isn't one. -->

**What the gap actually means:**
<!-- If the baseline matched your trained model, your fine-tuning added
     nothing — and that is a real finding, not a failure. Say it plainly. -->



---

## Run Log — Before

<!-- Five criteria across three seeds. The notebook's section 6 prints the
     spread table; the Target and Verdict columns are yours. -->

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |
| 2.  |  |  |  |  |  |
| 3.  |  |  |  |  |  |
| 4.  |  |  |  |  |  |
| 5.  |  |  |  |  |  |

### Confusion matrix

<!-- ⚠️ TYPED AS A MARKDOWN TABLE. The notebook prints one ready to paste.
     An image of a matrix earns nothing. -->

| true \ predicted |  |  |  |
|---|---|---|---|
| **** |  |  |  |
| **** |  |  |  |
| **** |  |  |  |

**My biggest off-diagonal number, and what it means:**
<!-- Not "the model made mistakes" — WHICH boundary it didn't learn, and which
     direction. "7 real analysis posts were called hot_take and only 3 went the
     other way" is a direction, not just an error rate. -->



---

## Verdicts and Diagnoses

<!-- MET or MISSED against LAST UNIT's target. The target has to hold across
     all three seeds, not turn up sometimes. -->

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 |  |  |  |  |
| 2 |  |  |  |  |
| 3 |  |  |  |  |
| 4 |  |  |  |  |
| 5 |  |  |  |  |

**Diagnoses**

<!-- For each miss: the cause, and how you know. The four common causes are:
     too few examples for a label, a boundary you applied inconsistently, a
     genuinely hard label pair, and a task the model can't reach from this
     much data.

     ⚠️ Use your agreement report as evidence. It is the only instrument you
     have that can tell a LABELLING problem from a MODEL problem, and this
     section is graded on whether you used it that way. -->



---

## Agreement Report

<!-- Your rate against the staff set, and every disagreement adjudicated.

     Remember you labelled these 30 under the STAFF taxonomy in
     data/staff_taxonomy.md, not your own — so every argument below is made
     from those definitions and those decision rules. -->

**Agreement rate:** ___ / 30 = ___%

<!-- Nobody grades this number. A 60% who argues every disagreement from the
     stated rules beats a 95% who wrote "staff was right" nine times. Several
     of the 30 were chosen because they're genuinely ambiguous — you should be
     winning some of these. -->

**Disagreements**

<!-- Three lines each: the post, both labels, and who you think is right and
     why — grounded in the staff definitions you were both applying.

     Then sort each into one of three piles:
       (a) the rule covered it and I applied it loosely → a consistency problem
       (b) the rule genuinely doesn't say               → a gap in the definitions
       (c) the rule is ambiguous here and my reading is defensible → argue it.
           This is a legitimate win.

     Pile (a) is the one that matters most for your diagnosis: if you applied a
     written rule two different ways on 30 posts, that is direct evidence about
     what you did across your own 200. -->

**1.**
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**2.**
> *The post:*
>
> *Staff said / I said:*
>
> *My call, and why:*
>
> *Which pile:*

**What the pattern in my disagreements tells me:**



---

## The Improvement

**What I changed:**

**Which diagnosis pointed at it:**

### Run Log — After

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1.  |  |  |  |  |  |
| 2.  |  |  |  |  |  |
| 3.  |  |  |  |  |  |
| 4.  |  |  |  |  |  |
| 5.  |  |  |  |  |  |

**Did it help, and how do I know:**

<!-- If it didn't, say so. Relabelling that didn't help is a genuinely
     interesting result and earns full credit. -->



---

## What's Still Broken

<!-- For each criterion still missed: what you'd do, and why you stopped. -->



**The gap between what I meant my labels to capture and what the model
learned:**
<!-- Two sentences. Your confusion matrix is the evidence. -->



<!-- ═════════════════════════════════════════════════════════════════════

     SUBMISSION CHECKLIST — unit 5

       [ ] criteria.md has five numbered criteria, each naming a NUMBER
       [ ] Each has a reason underneath tied to your data or taxonomy
       [ ] labels.csv: at least 150 rows, text/label/note, ONE file not split
       [ ] No label above 70%
       [ ] All five unit 5 sections have real content
       [ ] Label Taxonomy includes the decision rule for your hardest boundary
       [ ] The Dataset includes three hard cases
       [ ] results.json and test_split.csv committed (the notebook does this)
       [ ] At least four new commits
       [ ] Repository URL submitted — WRITE IT DOWN

     SUBMISSION CHECKLIST — unit 6

       [ ] Baseline vs. Trained table, with your prediction written beforehand
       [ ] Run Log — Before, five criteria across three seeds
       [ ] Confusion matrix TYPED AS A MARKDOWN TABLE
       [ ] A verdict on every criterion
       [ ] A diagnosis for every miss, using the agreement report as evidence
       [ ] Agreement Report with every disagreement adjudicated
       [ ] One improvement, with Run Log — After
       [ ] What's Still Broken
       [ ] results_three_seeds_before.json, results_three_seeds_after.json,
           baseline_results.json and
           agreement_results.json committed
       [ ] At least four new commits
       [ ] The SAME repository URL as last unit

     Do not delete and recreate this repository.
     ═════════════════════════════════════════════════════════════════════ -->

---

📖 **How to run this project: [RUNNING.md](RUNNING.md)**
