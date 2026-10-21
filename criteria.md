# Acceptance criteria — TakeMeter

Five criteria that say what "working" means for this classifier, written in
unit 5 **before** anything was trained.

**All five are yours this time.** None are given. You've had two projects of
practice.

An acceptance criterion names a number. *"The model is accurate"* is an
opinion. *"Every label has an F1 of at least 0.60 on the held-out set"* is a
criterion.

Under each, write a sentence or two on **why that number**. A reason that says
something about your data or your taxonomy earns credit — *"I picked 0.60 F1
for `reaction` because it's my smallest label and I only have about 50
examples of it"*. A reason that could be attached to any project does not.

> Missing your own targets next unit costs you nothing. Setting a target so
> easy you can't miss it does.

---

## Pick numbers you can defend

Not numbers that sound impressive. Three labels means a coin-flip guesser gets
about 33%, so a target of 0.40 is barely a target. Your number should sit
somewhere you'd honestly call useful.

**Cover at least three of these five areas.** They're here as prompts, not as a
form to fill in — a criterion that fits none of them is fine if it names a
number.

| Area | A question it could answer |
|---|---|
| Overall accuracy | How often does it need to be right to be worth using? |
| Per-label performance | Is one label allowed to be much worse than the others? |
| Balance | How lopsided can your label counts get before it's a problem? |
| Consistency | If someone else labelled the same posts, how often should you agree? |
| Confidence | Should a confident prediction be right more often than an unsure one? |

Two things worth knowing before you pick numbers, because both will affect
whether you hit them:

- **Your smallest label will have the jumpiest score.** If a label has 50
  examples, about 8 land in the test split. An F1 computed on 8 examples moves
  a lot between seeds. A target for that label should be looser than one for
  your biggest label, and saying so is a good reason.
- **Unit 6 tests across three seeds, and the target has to hold across all
  three.** A target of 0.65 against results of 0.71, 0.62, 0.68 is a **miss**.
  Pick with that in mind — it is stricter than it first sounds.

---

# TakeMeter — Acceptance Criteria

## Project Overview

TakeMeter is a text classification project that categorizes posts from r/personalfinance into four financial topics:

- `debt_credit`
- `saving_budgeting`
- `investing_retirement`
- `financial_services_other`

The following acceptance criteria define the minimum standards for the dataset and classifier. These targets are established before model training.

## 1. Overall Classification Accuracy

**Target:** At least 80% accuracy on the held-out test dataset.

**Measurement:** Divide the number of correctly classified posts by the total number of test posts.

**Rationale:** I chose 80% because I want TakeMeter to correctly identify the primary financial topic for most posts, while recognizing that some posts discuss multiple financial issues and may be difficult to classify.

## 2. Per-Label Recall

**Target:** At least 70% recall for each of the four labels.

**Measurement:** Calculate recall independently for each category using:

Recall = True Positives / (True Positives + False Negatives)

**Rationale:** I chose 70% because overall accuracy alone might hide poor performance on less common categories. I want the classifier to recognize posts across all four financial topics rather than favoring one category.

## 3. Dataset Class Balance

**Target:** No single label should represent more than 40% of the 200-post dataset.

**Measurement:** Count the number of posts assigned to each label. Each category must contain no more than 80 posts.

**Rationale:** I chose this threshold to prevent one category from dominating the dataset. Maintaining a reasonable distribution should help the model learn distinctions between financial topics instead of relying too heavily on the most common label.

## 4. Independent Human Annotation Agreement

**Target:** At least 85% agreement between two independent human annotators on a shared sample of 40 posts.

**Measurement:** Both annotators independently classify the same 40 posts using the finalized label definitions. Agreement is calculated as:

Agreement = (Number of Identically Labeled Posts / 40) × 100

At least 34 of the 40 posts must receive matching labels.

**Rationale:** I chose 85% because I want the label definitions to be understandable and consistently applied by different people. High agreement would provide evidence that the categories are sufficiently clear for reliable annotation.

> **Revised before training:** At least 85% agreement between my labels and the staff labels on the 30-post staff set (`data/staff_posts.csv`), labelled under the staff taxonomy in `data/staff_taxonomy.md` and measured with `agreement.py`. At least 26 of the 30 posts must match.
>
> **Why revised:** The original needed a second independent annotator, and I don't have one, so it could not be measured. The staff set is the agreement check the course provides. I kept the 85% target: if I can't apply someone else's written rules that consistently, I have less reason to trust that my own 200 labels are consistent.

## 5. High-Confidence Prediction Accuracy

**Target:** At least 85% accuracy among test predictions with a confidence score of 0.80 or higher.

**Measurement:** Identify held-out test predictions where the model's highest predicted class probability is at least 0.80. Calculate the percentage of those predictions that are correct.

If no predictions reach the confidence threshold, this criterion is not met. The number and percentage of qualifying predictions will also be reported.

**Rationale:** I chose this criterion because I want predictions that the model considers highly confident to be more reliable. It will help me evaluate whether confidence scores are useful for distinguishing clearer classifications from uncertain ones.

> **Revised before training:** At least 85% accuracy on the most-confident third of held-out test predictions, as reported by the notebook (`confidence.accuracy_most_confident_third` in `results.json`).
>
> **Why revised:** The notebook does not record per-prediction confidence, so the 0.80 cutoff could not be checked against its output. It does report accuracy for the most- and least-confident thirds. I kept the 85% target, and the question is the same: when the model is most sure, is it right?

## Evaluation Plan

- **Dataset:** 200 unique, eligible Reddit posts with manually reviewed labels.
- **Data split:** 70% training, 15% validation and 15% testing, stratified by label (the notebook's split).
- **Test set:** 30 posts, roughly 6–9 per label.
- **Human agreement sample:** The 30-post staff set, compared with `agreement.py` (see criterion 4).
- **Seeds:** In unit 6 each target must hold on all three seeds.
- **Evaluation:** All five acceptance criteria will be assessed against their predefined numerical targets.
- **Timing:** These criteria will be finalized and committed before model training begins.

## Success Definition

TakeMeter will meet its acceptance criteria only when all five targets are satisfied. Any unmet criterion will be documented in the final project evaluation rather than changing its target after reviewing the results.

<!-- ─────────────────────────────────────────────────────────────────────────
     UNIT 6 — read this before you change anything above.

     If a criterion turns out to be BROKEN rather than merely unmet, you can
     revise it, and that earns credit. But never delete or edit the original
     line. Add the revision underneath, like this:

         ## 2. Every label performs acceptably

         The model performs well on all labels.

         **Why this target:** ...

         > **Revised in unit 6:** Every label has an F1 of at least 0.60 on
         > the held-out set.
         >
         > **Why revised:** "performs well" gave me nothing to check. I
         > couldn't produce a verdict from it at all.

     That's a revision because the criterion couldn't be MEASURED.

     Lowering a target because you missed it is not a revision, and it costs
     you the point:

         ✗ "Overall accuracy of at least 0.65" → "at least 0.55", because
            0.65 turned out to be optimistic for 200 examples.

     A number you missed stays where it is, gets diagnosed, and gets a fix
     attempted. That's where the points are.
     ───────────────────────────────────────────────────────────────────────── -->
