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

This project is a text classifier designed to sort posts from the **r/sustainability** community. Online environmental communities often mix practical tips, high-level economic arguments, and scientific data drops. This model automatically categorizes posts into one of three distinct lanes to help users filter between actionable daily habits, broad systemic discussions, and hard news.

---

## Label Taxonomy

### `personal_action`

**Definition:** A post asking for or sharing practical, individual tips for reducing one's environmental footprint at home or work.

**Example 1:**
> "I tried a 10-litre bucket bathe — it cut my shower water use by about 80%."

**Example 2:**
> "I force myself to use the local train 4-5 days a week, instead of driving the 8 miles to uni."

### `systemic_debate`

**Definition:** An argument or discussion focusing on large-scale corporate practices, government policies, or global economic systems.

**Example 1:**
> "Cars are an environmental catastrophe no matter what they’re burning (or not)."

**Example 2:**
> "The hard part is literally getting the petroleum usage out of the entire supply chain, not just the end of it."

### `news_and_data`

**Definition:** A post sharing or summarizing a specific new scientific study, climate metric, or recent environmental news event.

**Example 1:**
> "So a 2024 study suggests that would take almost 500 years to exhaust earth’s phosphorus reserves."

**Example 2:**
> "The rare earth thing is already gone from 2028 battery chemistries and the weights will get better by 2030."

### The hardest boundary

**Which two labels:** `personal_action` and `systemic_debate`

**The decision rule I used every time:**
If the ultimate goal of the post is to persuade the reader to change a daily habit or make a personal purchase (like buying an e-bike or changing shower habits), it is `personal_action`—even if 80% of the post is made up of global data and studies.

---

## The Dataset

**Where the posts came from:** Public threads on the `r/sustainability` subreddit.

**How I labelled them:** I labeled the first 20 posts completely cold. For the remaining 184, I used an AI tool for pre-labeling based on my strict taxonomy, followed by a mandatory manual review and correction of every single row.

**Counts per label:**

| Label | Count | Share |
|---|---|---|
| `personal_action` | 111 | 54.4% |
| `systemic_debate` | 73 | 35.8% |
| `news_and_data` | 20 | 9.8% |
| **Total** | 204 | 100% |

**Three hard cases**

**1.**
> *The post:* "I tried a 10-litre bucket bathe — it cut my shower water use by about 80%... My motivation is this: drought pressure, increasingly dry summers, and water bills creeping up."
>
> *Could have been:* `personal_action` or `systemic_debate`
>
> *I chose, because:* `personal_action`. Applying my decision rule, the macro-level environmental data was subordinated to the personal call-to-action of changing a daily household habit.

**2.**
> *The post:* "This is fair. But I don’t think the answer is to stop these. The answer is public education. 1. Make sure that people are wearing proper safety equipment... I hae a brain injury and most of the people I know with brain injuries got them from car accidents."
>
> *Could have been:* `personal_action` or `systemic_debate`
>
> *I chose, because:* `systemic_debate`. Despite the personal medical story, the solutions demanded ("public education" and "proper infrastructure") require government intervention and city planning.

**3.**
> *The post:* "So a 2024 study suggests that would take almost 500 years to exhaust earth’s phosphorus reserves. I think this can safely be left for future generations who will have abundant energy, automation..."
>
> *Could have been:* `news_and_data` or `systemic_debate`
>
> *I chose, because:* `systemic_debate`. The 2024 study was merely a jumping-off point for a sweeping philosophical argument about the economy and future societies, rather than a straightforward data summary.

---

## The Training Run

- **Hardware / Device:** CPU (`torch_version: 2.14.1+cpu`)
- **Settings Used:** Default settings — 3 epochs, learning rate `2e-5`, batch size 16, and maximum length of 128 tokens per post.
- **Modifications:** None. The default settings were sufficient for a baseline run on a laptop CPU.

---

## How I Used AI

**Moment 1**

- *What I asked for:* I provided my exact taxonomy and decision rules, and asked the AI to format batches of raw Reddit text into a CSV structure with proposed labels.
- *What came back:* Raw CSV blocks with `text`, `label`, and `note` columns filled out based on my definitions.
- *What I changed:* I manually reviewed every single row and corrected any labels where the AI misunderstood the nuance of my boundary rules before pasting them into my file.

**Moment 2**

- *What I asked for:* I asked the AI to argue the strongest case for the two most likely labels for my three "hard cases" without telling me the final answer.
- *What came back:* A breakdown of why each borderline post could fit into two different categories based on my definitions.
- *What I changed:* I used this debate to clarify my own decision rule and firmly document exactly why I ultimately chose the label I did.

**Pre-labelling disclosure:**
I labeled my first 20 posts completely cold to establish a baseline. I then used an AI model to pre-label the remaining 184 posts. I read, verified, and manually corrected every single one of those pre-labeled rows before using them in my dataset.

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy | 0.323 | 0.677 | +0.355 |
| Macro F1 | 0.258 | 0.400 | +0.142 |
| F1 — `news_and_data` | 0.160 | 0.000 | -0.160 |
| F1 — `personal_action` | 0.615 | 0.800 | +0.185 |
| F1 — `systemic_debate` | 0.000 | 0.400 | +0.400 |

**What I predicted before I looked:**
I predict my trained model will vastly outperform the baseline specifically on the systemic_debate label, because the zero-shot baseline completely failed to identify it (scoring 0.000) using only the text definitions.
**What the gap actually means:**
<!-- If the baseline matched your trained model, your fine-tuning added
     nothing — and that is a real finding, not a failure. Say it plainly. -->
The trained model's `systemic_debate` F1 rose from 0.000 to 0.400, suggesting that fine-tuning helped it learn nuanced boundaries in this subreddit's language that the zero-shot model could not infer from definitions alone. The gains were not uniform—the `news_and_data` F1 fell from 0.160 to 0.000—so the results support better recognition of some subreddit-specific patterns rather than proving that fine-tuning solved every boundary.

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