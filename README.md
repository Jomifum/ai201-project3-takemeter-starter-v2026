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
| 1. Dataset Size | At least 200 labeled examples in total | 204 | 204 | 204 | MET |
| 2. Class Balance | No single label makes up more than 60% of the dataset | 56.9% max (`personal_action`) | 56.9% max (`personal_action`) | 56.9% max (`personal_action`) | MET |
| 3. `personal_action` Recall | Recall for `personal_action` must be > 0.75 | 1.000 | 0.944 | 0.944 | MET |
| 4. Baseline F1 Beat | Fine-tuned macro F1 must beat zero-shot baseline macro F1 by at least 0.05 | +0.142 (0.400 vs. 0.258) | +0.201 (0.460 vs. 0.258) | +0.193 (0.452 vs. 0.258) | MET |
| 5. Confidence Gap | Accuracy of the most confident third must be at least 20% higher than the least confident third | 1.000 vs. 0.600 (1.67x) | 1.000 vs. 0.400 (2.50x) | 1.000 vs. 0.300 (3.33x) | MET |

### Confusion matrix

<!-- ⚠️ TYPED AS A MARKDOWN TABLE. The notebook prints one ready to paste.
     An image of a matrix earns nothing. -->

| true \ predicted | personal_action | systemic_debate | news_and_data |
|---|---|---|---|
| **personal_action** | 18 | 0 | 0 |
| **systemic_debate** | 8 | 3 | 0 |
| **news_and_data** | 1 | 1 | 0 |

**My biggest off-diagonal number, and what it means:**
<!-- Not "the model made mistakes" — WHICH boundary it didn't learn, and which
     direction. "7 real analysis posts were called hot_take and only 3 went the
     other way" is a direction, not just an error rate. -->
The largest error is 8 real `systemic_debate` posts classified as `personal_action`, while 0 `personal_action` posts were classified as `systemic_debate`, showing a strong lean toward `personal_action` on this boundary; the agreement report can help determine whether this reflects inconsistent labels or a model limitation.

---

## Verdicts and Diagnoses

<!-- MET or MISSED against LAST UNIT's target. The target has to hold across
     all three seeds, not turn up sometimes. -->

| # | Criterion | Target | Verdict | How I decided |
|---|---|---|---|---|
| 1 | Dataset Size | At least 200 labeled examples | MET | All three runs used 204 examples, above the 200-example target. |
| 2 | Class Balance | No label above 60% | MET | `personal_action` was the largest class at 56.9% in all three runs, below 60%. |
| 3 | `personal_action` Recall | Greater than 0.75 | MET | Recall was 1.000, 0.944, and 0.944 across the three seeds; every run exceeded 0.75. |
| 4 | Baseline F1 Beat | Macro F1 at least 0.05 above the zero-shot baseline | MET | The trained model beat the 0.258 baseline by 0.142, 0.201, and 0.193 across the seeds. |
| 5 | Confidence Gap | Most-confident-third accuracy at least 20% higher than least-confident-third accuracy | MET | The most-confident third scored 1.000 for each seed, versus 0.600, 0.400, and 0.300 for the least-confident third. |

**Diagnoses**

<!-- For each miss: the cause, and how you know. The four common causes are:
     too few examples for a label, a boundary you applied inconsistently, a
     genuinely hard label pair, and a task the model can't reach from this
     much data.

     ⚠️ Use your agreement report as evidence. It is the only instrument you
     have that can tell a LABELLING problem from a MODEL problem, and this
     section is graded on whether you used it that way. -->

No criterion was missed. The largest confusion is a directional 8-to-0 error: 8 real `systemic_debate` posts were called `personal_action`, while no `personal_action` posts were called `systemic_debate` (seed 42). The agreement check found 30/30 matches and no disagreements, so it gives no evidence of inconsistent labeling in that staff sample; this points more toward a difficult boundary or a model bias toward `personal_action` than a demonstrated labeling inconsistency. The agreement set uses a different taxonomy, however, so it cannot directly confirm how consistently I applied the `systemic_debate`/`personal_action` boundary in my training data.


---

## Agreement Report

<!-- Your rate against the staff set, and every disagreement adjudicated.

     Remember you labelled these 30 under the STAFF taxonomy in
     data/staff_taxonomy.md, not your own — so every argument below is made
     from those definitions and those decision rules. -->

**Agreement rate:** 30 / 30 = 100%

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

None. The checked-in staff and my labels match for all 30 posts, so there are no disagreements to adjudicate.

**What the pattern in my disagreements tells me:**

There were no disagreements: I matched the staff label on all 30 posts, including the cases marked borderline in the staff file. That gives no evidence of inconsistency on this sample or of a recurring mistake pattern; it also means this report cannot identify which boundary drove errors in my own training data.


---

## The Improvement

**What I changed:** I added 15 high-quality `systemic_debate` examples to `labels.csv`, bringing the dataset from 204 to 219 examples.

**Which diagnosis pointed at it:** In the seed 42 confusion matrix, 8 real `systemic_debate` posts were misclassified as `personal_action`, while 0 `personal_action` posts were misclassified as `systemic_debate`. That one-way 8-to-0 error suggested the model needed more examples of the under-recognized `systemic_debate` class.

### Run Log — After

| Criterion | Target | Seed 42 | Seed 7 | Seed 2024 | Verdict |
|---|---|---|---|---|---|
| 1. Dataset Size | At least 200 labeled examples | 219 | 219 | 219 | MET |
| 2. Class Balance | No single label makes up more than 60% | 53.0% max (`personal_action`) | 53.0% max (`personal_action`) | 53.0% max (`personal_action`) | MET |
| 3. `personal_action` Recall | Greater than 0.75 | 1.000 | 0.882 | 0.941 | MET |
| 4. Baseline F1 Beat | Macro F1 at least 0.05 above the zero-shot baseline (0.258) | +0.275 (0.533 vs. 0.258) | +0.325 (0.583 vs. 0.258) | +0.300 (0.558 vs. 0.258) | MET |
| 5. Confidence Gap | Most-confident-third accuracy at least 20% higher than least-confident-third accuracy | 1.000 vs. 0.636 (1.57x) | 1.000 vs. 0.818 (1.22x) | 1.000 vs. 0.727 (1.38x) | MET |

**Did it help, and how do I know:**

<!-- If it didn't, say so. Relabelling that didn't help is a genuinely
     interesting result and earns full credit. -->

Yes. Average accuracy rose from 0.699 before to 0.818 after (+0.119), and average macro F1 rose from 0.437 to 0.558 (+0.121); both metrics improved for each seed. The new data changed the test split (31 examples before versus 33 after), so this is encouraging evidence of improvement rather than a perfectly controlled comparison.


---

## What's Still Broken

<!-- For each criterion still missed: what you'd do, and why you stopped. -->

No criteria were missed in the after-run. The model still scored 0.000 F1 for `news_and_data` on all three seeds, though that class had only two test examples per seed; `systemic_debate` errors also remain, with 5, 1, and 3 posts called `personal_action` for seeds 42, 7, and 2024.

**The gap between what I meant my labels to capture and what the model
learned:**
<!-- Two sentences. Your confusion matrix is the evidence. -->

The extra examples improved the `systemic_debate` boundary: its F1 rose from 0.400, 0.588, and 0.526 before to 0.750, 0.867, and 0.786 after, and the number of `systemic_debate` posts called `personal_action` fell from 8, 6, and 6 to 5, 1, and 3. The model still confuses some structural arguments with individual actions and fails to recognize the rare `news_and_data` class in these small test sets.


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