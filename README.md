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

TakeMeter classifies posts from r/personalfinance by the main financial question the author is asking. Each post gets exactly one of four labels: `debt_credit`, `saving_budgeting`, `investing_retirement`, or `financial_services_other`. Many posts mention several topics at once (savings *and* a credit card, a 401(k) *and* a tax bill), so the label follows the decision the author wants help with, not every financial term in the post. The classifier is a fine-tuned DistilBERT model trained on 200 posts I collected and labelled.

---

## Label Taxonomy

The full taxonomy, including an AI-generated stress test of the boundaries, is in [data/finance_taxonomy.md](data/finance_taxonomy.md).

### `debt_credit`

**Definition:** The post is mainly about existing debt, borrowing, loan repayment, interest rates, credit cards, or credit history.

**Example 1:**
> *Capital One refused hardship, interest reduction, and settlement. What options do I have left?* ([1xawqqp](https://www.reddit.com/r/personalfinance/comments/1xawqqp/)). The author has credit card debt and was denied hardship relief, so the question is how to repay it.

**Example 2:**
> *Is a balance transfer my best option?* ([1xb3u0a](https://www.reddit.com/r/personalfinance/comments/1xb3u0a/)). The author wants to cut credit card interest with a balance transfer.

### `saving_budgeting`

**Definition:** The post is mainly about managing income and expenses, savings goals, emergency funds, or whether something is affordable.

**Example 1:**
> *Can I afford $1000 rent under these circumstances?* ([1xaw18c](https://www.reddit.com/r/personalfinance/comments/1xaw18c/)). The author lists income and expenses and asks whether the rent fits.

**Example 2:**
> *Anxiety With Buying a Home* ([1xa9708](https://www.reddit.com/r/personalfinance/comments/1xa9708/)). The author compares renting with buying, based on income, savings and expected housing costs.

### `investing_retirement`

**Definition:** The post is mainly about investment decisions, investment accounts, asset allocation, or long-term retirement planning.

**Example 1:**
> *401k investing but don't understand* ([1xawpn3](https://www.reddit.com/r/personalfinance/comments/1xawpn3/)). The author wants help understanding the fund choices and performance in their 401(k).

**Example 2:**
> *How can I retire early?* ([1xad68f](https://www.reddit.com/r/personalfinance/comments/1xad68f/)). The author just started a higher-paying job and asks how to plan for early retirement.

### `financial_services_other`

**Definition:** The post is mainly about taxes, insurance, banking procedures, employment benefits, or other financial paperwork that doesn't fit the first three labels.

**Example 1:**
> *State garnished my bank account for back taxes* ([1xb9g5m](https://www.reddit.com/r/personalfinance/comments/1xb9g5m/)). The author is trying to resolve a state tax collection and find the records to support their case.

**Example 2:**
> *Baby on the way, should I get life insurance through my work?* ([1xargmo](https://www.reddit.com/r/personalfinance/comments/1xargmo/)). The author compares employer life insurance with buying a policy elsewhere.

### The hardest boundary

**Which two labels:** `saving_budgeting` and `debt_credit`. Posts constantly weigh savings against debt ("should I use my emergency fund to pay off my card?"), and both labels fit on the surface. A close second is `investing_retirement` vs. `financial_services_other` for retirement accounts: rollovers, HSAs and pensions are partly investing and partly paperwork.

**The decision rule I used every time:**
Label the decision the author is asking for help with, not the topics they mention. If the action is repaying or taking on debt (paying down a card, taking a loan to build credit), it's `debt_credit`, even if savings are involved. If the question is whether a cost fits their income or how to split up cash, it's `saving_budgeting`. For retirement accounts: choosing what to invest in or how much to contribute is `investing_retirement`. Eligibility, contribution rules, transfers and paperwork are `financial_services_other`.

---

## The Dataset

**Where the posts came from:** r/personalfinance, from the subreddit's public RSS feed (`/r/personalfinance.rss` and `/new.rss`), posted between October 15 and 20, 2026. I kept only posts with body text; crossposts that showed only a title were skipped. Every post is stored whole as `title + body`, with its Reddit ID in the `note` column. There are no duplicates.

**How I labelled them:** I labelled the first 20 posts myself, without AI suggestions (`note` = `cold`). The other 180 were pre-labelled by an AI model using my definitions and decision rule, and I kept the AI's suggested labels (`note` = `AI-suggested`). 33 of the borderline posts have a `REVIEW:` note naming the two labels in tension.

**Counts per label:**

| Label | Count | Share |
|---|---|---|
| `saving_budgeting` | 57 | 28.5% |
| `investing_retirement` | 56 | 28.0% |
| `debt_credit` | 47 | 23.5% |
| `financial_services_other` | 40 | 20.0% |
| **Total** | 200 | 100% |

No label exceeds 40% of the dataset (criterion 3).

**Three hard cases**

**1.**
> *The post:* "Mom died, she left me money. Idk what to really do" ([1xb1omh](https://www.reddit.com/r/personalfinance/comments/1xb1omh/)). A 28-year-old has $200k in life insurance, $100k in investments, and their mom's $5–10k of credit card debt. They want to "stretch that out as long as we can / only use it for emergencies."
>
> *Could have been:* `financial_services_other` (life insurance, the estate, who pays the mom's card debt) or `saving_budgeting`.
>
> *I chose `saving_budgeting`, because:* the author says the goal is to make the money last and keep it for emergencies. The insurance and estate questions are steps toward that goal. The AI suggested `financial_services_other`; I kept my own label.

**2.**
> *The post:* "First car: Financing a car VS paying cash" ([1xau77y](https://www.reddit.com/r/personalfinance/comments/1xau77y/)). A 23-year-old has enough savings to buy a used car outright but is thinking of taking a loan to build credit.
>
> *Could have been:* `saving_budgeting` (spending savings on a big purchase) or `debt_credit`.
>
> *I chose `debt_credit`, because:* they can already afford the car. The real question is whether borrowing will help their credit history.

**3.**
> *The post:* "Should I stay at a government job that I'm not happy at just because it has a pension?" ([1x9v7m0](https://www.reddit.com/r/personalfinance/comments/1x9v7m0/)). A 29-year-old in local government with a state pension and a 14% match is comparing it with private jobs that offer a 401(k) match.
>
> *Could have been:* `financial_services_other` (a pension is an employment benefit) or `investing_retirement`.
>
> *I chose `investing_retirement`, because:* the decision they're weighing is which retirement setup leaves them better off long term. The job itself is the context, not the question.

---

## The Training Run

**Base model:** `distilbert-base-uncased`

**Settings:** 3 epochs, learning rate 2e-5, batch size 16, max length 128 tokens, seed 42. These are the notebook's defaults. It ran on CPU (`torch 2.14.1+cpu`) and took about 2 minutes.

**Anything I changed from the defaults, and why:**
Nothing yet. The default run left the model undertrained. Training loss only went from 1.38 to 1.35. A model that guesses evenly among four labels has a loss of about 1.386, so this one barely got past guessing. Validation accuracy was 0.45, test accuracy was 0.367 (macro F1 0.23), and it never predicted `debt_credit` or `financial_services_other`. With 139 training posts at batch size 16, 3 epochs is only 27 gradient steps. My next run raises the epochs and the learning rate, and I'll judge that change by validation accuracy, not the test score. <!-- TODO: after the retrain, record the new settings and the val/test numbers here. -->

**Split sizes:** 139 train / 31 val / 30 test (70/15/15, stratified, seed 42).

| Label | Train | Val | Test |
|---|---|---|---|
| `debt_credit` | 33 | 7 | 7 |
| `saving_budgeting` | 39 | 9 | 9 |
| `investing_retirement` | 39 | 9 | 8 |
| `financial_services_other` | 28 | 6 | 6 |

Two labels have fewer than 8 test posts: `debt_credit` (7) and `financial_services_other` (6). One misclassified post moves their recall by 14–17 points, so I expect their scores to swing between seeds in unit 6.

---

## How I Used AI

**Moment 1**

- *What I asked for:* I had 187 labelled posts and asked Claude (Claude Code) to create 13 more posts to reach 200.
- *What came back:* It declined to write synthetic posts. My criteria and taxonomy require 200 *real* Reddit posts, and posts written to fit my definitions would inflate the results. Instead it pulled posts from r/personalfinance RSS pages I pasted in. It checked each post's Reddit ID against `labels.csv` (the first page was almost all duplicates) and added 13 new posts, mostly to the two smallest labels, each with a suggested label.
- *What I changed:* I supplied the real feed pages instead of accepting generated text. The 13 posts are marked `AI-suggested` like the rest.

**Moment 2**

- *What I asked for:* Before writing the labels, I had an AI generate eight short ambiguous posts to stress-test my taxonomy ([finance_taxonomy.md](data/finance_taxonomy.md), section 4).
- *What came back:* Posts that sat exactly on my boundaries, such as "use my emergency fund to pay off my card?" and "increase emergency savings or contribute more to my 401(k)?"
- *What I changed:* I added four decision rules (section 5 of the taxonomy): label the main question; label the requested action, not the account type; keep immediate budgeting separate from retirement strategy; and use `financial_services_other` only for genuine admin, tax, insurance or benefit questions, not as a catch-all. The synthetic posts are not in the dataset.

**Pre-labelling disclosure:** Yes. 20 posts were labelled cold by me with no AI input. The other 180 were pre-labelled by an AI model, and those labels were kept as suggested. The `note` column says which is which: `cold` or `AI-suggested`. Where I recorded an AI suggestion that differed from my cold label (1xb1omh, hard case 1), I kept my own label. Because 90% of the labels are AI-suggested, the staff agreement check in unit 6 (criterion 4) is the main evidence of whether they're applied consistently. Claude Code also helped set up the notebook, revise criteria 4 and 5 before training (see `criteria.md`), and run the first training pass.

<!-- ═══════════════════════ UNIT 6 — THE TEST ═══════════════════════

     Don't fill these in during unit 5.
     ═══════════════════════════════════════════════════════════════════ -->

---

## Baseline vs. Trained

<!-- Both models on the same posts. `python baseline.py --trained results.json`
     prints this table for you. -->

Baseline: `facebook/bart-large-mnli` zero-shot, prompted with the definitions in `label_definitions.txt`, scored on the 30 posts in `test_split.csv`. The trained model was tested on the same 30 posts (results in `baseline_results.json`).

| Measure | Baseline | Trained | Difference |
|---|---|---|---|
| Overall accuracy | 0.467 | 0.367 | -0.100 |
| Macro F1 | 0.411 | 0.230 | -0.181 |
| F1 — `debt_credit` | 0.500 | 0.000 | -0.500 |
| F1 — `financial_services_other` | 0.000 | 0.000 | +0.000 |
| F1 — `investing_retirement` | 0.714 | 0.538 | -0.176 |
| F1 — `saving_budgeting` | 0.429 | 0.381 | -0.048 |

**What I predicted before I looked:**
<!-- Milestone 1 asks you to write this BEFORE seeing the trained numbers. A
     prediction made afterwards isn't one. -->

My trained model is barely past guessing and never predicts `debt_credit` or `financial_services_other`, so I expect it to beat the zero-shot baseline only on `investing_retirement` (it predicts that label most of the time) and to lose on overall accuracy and macro F1.

**What the gap actually means:**
<!-- If the baseline matched your trained model, your fine-tuning added
     nothing — and that is a real finding, not a failure. Say it plainly. -->

The fine-tuning added nothing yet. The zero-shot baseline beats my trained model on overall accuracy, macro F1 and every label except `financial_services_other`, where both score 0. My prediction was wrong on `investing_retirement`: the baseline beat it there too (0.714 vs. 0.538). That fits the Run Log: the default run barely trained (loss 1.38 → 1.35) and only ever predicted two labels. The baseline has its own bias. It put 19 of 30 posts in `saving_budgeting` and never predicted `financial_services_other`. So the result says my model is undertrained, not that the task is easy.

**What my definitions were worth:** I ran the baseline twice on the same 30 posts, once with my definitions and once with only the bare label names:

| Prompt | Accuracy | Macro F1 |
|---|---|---|
| My definitions (`label_definitions.txt`) | 0.467 | 0.411 |
| Bare label names (no definitions file) | 0.700 | 0.689 |

My definitions made the baseline worse by 0.233 accuracy (7 posts). With bare names, the predictions spread across all four labels (9/8/8/5). With my definitions, most posts went to `saving_budgeting`. The likely reason: each definition lists five or six loosely related topics. Almost any personal-finance post "discusses income, expenses or affordability," so the broad `saving_budgeting` list entails nearly everything. The bare name `financial_services_other` also did much better than my definition, which ends in a vague "other financial administrative matters." The prompt is fragile too: rewording two definitions to match this README exactly gave 0.400 accuracy instead of 0.467. On 30 posts that is 2 posts, so differences this small are noise. The table above uses my definitions as committed. The stronger bare-names baseline (0.700) is the bar my retrained model has to clear.



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
