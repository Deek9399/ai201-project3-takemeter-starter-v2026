

## Milestone 2: Defining the Classification Labels

---

## 1. Label Definitions

### Label 1: `debt_credit`

**Definition:** Posts primarily discussing existing debt, borrowing, loan repayment, interest rates, credit cards, or credit history.

**Examples of topics covered:**
- Credit card debt and repayment strategies
- Student loans and personal loans
- Credit scores and credit-building strategies
- Debt consolidation and balance transfers
- Interest rates and borrowing decisions

### Label 2: `saving_budgeting`

**Definition:** Posts primarily discussing income management, expenses, savings goals, emergency funds, or the affordability of financial decisions.

**Examples of topics covered:**
- Monthly budgeting
- Emergency savings
- Rent and housing affordability
- Managing income and expenses
- Saving for major purchases

### Label 3: `investing_retirement`

**Definition:** Posts primarily discussing investment decisions, investment accounts, asset allocation, or long-term retirement planning.

**Examples of topics covered:**
- Stocks, ETFs, and index funds
- 401(k) and IRA investments
- Retirement savings strategies
- Investment portfolio allocation
- Retirement planning

### Label 4: `financial_services_other`

**Definition:** Posts primarily discussing taxes, insurance, banking procedures, employment benefits, or other financial administrative matters that do not fit the first three categories.

**Examples of topics covered:**
- Tax disputes and filing questions
- Life and health insurance
- Banking account issues
- Employment compensation and benefits
- Financial paperwork and administrative processes

---

## 2. Real Reddit Examples for Each Label

The following examples were selected from the collected r/personalfinance dataset.

### `debt_credit`

**Example 1: Capital One refused hardship, interest reduction, and settlement. what options do I have left?**

Reddit post: https://www.reddit.com/r/personalfinance/comments/1xawqqp/

The author has credit card debt and is seeking alternatives after being denied interest relief and hardship assistance.

**Reason for label:** The primary question concerns managing and repaying existing debt.

**Example 2: Is a balance transfer my best option?**

Reddit post: https://www.reddit.com/r/personalfinance/comments/1xb3u0a/

The author wants to reduce credit card interest charges and is considering a balance transfer.

**Reason for label:** The primary decision involves credit card debt repayment.

### `saving_budgeting`

**Example 1: can i afford $1000 rent under these circumstances?**

Reddit post: https://www.reddit.com/r/personalfinance/comments/1xaw18c/

The author provides information about income and expenses and asks whether a particular monthly rent is affordable.

**Reason for label:** The main question concerns budgeting and housing affordability.

**Example 2: Anxiety With Buying a Home**

Reddit post: https://www.reddit.com/r/personalfinance/comments/1xa9708/

The author compares renting and buying a home while considering income, savings, and expected housing costs.

**Reason for label:** The primary concern is whether the housing expense is financially manageable.

### `investing_retirement`

**Example 1: 401k investing but don’t understand**

Reddit post: https://www.reddit.com/r/personalfinance/comments/1xawpn3/

The author wants help understanding investment allocations and performance inside a 401(k).

**Reason for label:** The main question concerns retirement investments and portfolio choices.

**Example 2: How can I retire early?**

Reddit post: https://www.reddit.com/r/personalfinance/comments/1xad68f/

The author asks how to begin planning for early retirement after starting a higher-paying job.

**Reason for label:** The main objective is long-term retirement planning.

### `financial_services_other`

**Example 1: State garnished my bank account for back taxes**

Reddit post: https://www.reddit.com/r/personalfinance/comments/1xb9g5m/

The author is trying to resolve a state tax collection issue and locate supporting tax records.

**Reason for label:** The main question involves tax administration and documentation.

**Example 2: Baby on the way, should I get life insurance through my work?**

Reddit post: https://www.reddit.com/r/personalfinance/comments/1xargmo/

The author wants to compare employer-provided life insurance with other insurance options.

**Reason for label:** The main question concerns insurance coverage.

---

## 3. Borderline Case and Decision Rule

### Real Borderline Example

**Post: First car: Financing a car VS paying cash**

Reddit post: https://www.reddit.com/r/personalfinance/comments/1xau77y/

**Situation:**

The author has enough savings to purchase a used car outright but is considering taking out a car loan to build credit.

**Possible labels:**
- `saving_budgeting` — because the decision involves using savings for a purchase.
- `debt_credit` — because the author is asking whether borrowing money would help establish credit.

**Final label: `debt_credit`**

**Reasoning:**

Although savings are mentioned, the author's primary question concerns whether taking out a loan would be financially beneficial for building credit.

### General Decision Rule

When a post contains multiple financial topics, assign the label that best represents the author's primary question or requested decision.

Do not assign a label simply because a particular financial term appears in the post.

For example:
- Asking whether to take out a loan to build credit → `debt_credit`
- Asking whether a car purchase is affordable → `saving_budgeting`
- Asking which retirement funds to invest in → `investing_retirement`
- Asking how an insurance policy works → `financial_services_other`

---

## 4. AI-Generated Ambiguity Stress Test

To test the clarity of the taxonomy, eight synthetic examples were generated. These examples are used only to evaluate the labeling rules and are not included in the real Reddit dataset.

### Test 1

**Synthetic post:** I have $8,000 in savings and $6,000 in credit-card debt. Should I use my emergency fund to pay off the card?

**Assigned label:** `debt_credit`

**Reason:** The primary decision concerns repaying existing debt.

### Test 2

**Synthetic post:** My student loans are paid off. Should I increase my emergency savings or start contributing more to my 401(k)?

**Assigned label:** `saving_budgeting`

**Reason:** The question compares an immediate savings goal with another use of available income.

**Ambiguity identified:** This could also be interpreted as retirement planning. The final decision depends on whether the main question is about allocating current cash or developing a retirement contribution strategy.

### Test 3

**Synthetic post:** I inherited $50,000. Which index funds should I invest in for the next 20 years?

**Assigned label:** `investing_retirement`

**Reason:** The primary question asks for a long-term investment strategy.

### Test 4

**Synthetic post:** My employer offers two health insurance plans. Which has better coverage for a planned surgery?

**Assigned label:** `financial_services_other`

**Reason:** The primary question concerns insurance coverage.

### Test 5

**Synthetic post:** I'm buying a car and can afford cash, but would financing help my credit score?

**Assigned label:** `debt_credit`

**Reason:** The primary question concerns borrowing and building credit.

### Test 6

**Synthetic post:** My bank froze my savings account and wants identity documents. What should I do?

**Assigned label:** `financial_services_other`

**Reason:** The main problem concerns a banking procedure rather than a savings strategy.

### Test 7

**Synthetic post:** I'm 45 with a mortgage and a 401(k). Should I change my retirement asset allocation to retire at 60?

**Assigned label:** `investing_retirement`

**Reason:** The main question concerns retirement investment allocation.

### Test 8

**Synthetic post:** I earn $4,000 a month, have a car payment, and want to know whether $1,600 rent is manageable.

**Assigned label:** `saving_budgeting`

**Reason:** The primary question concerns monthly housing affordability.

---

## 5. Taxonomy Refinements After Stress Testing

The ambiguity test showed that some posts contain overlapping financial topics. To improve consistency, the following rules were established.

**Rule 1: Prioritize the main question.**

The assigned label should reflect what the author primarily wants help deciding or resolving.

**Rule 2: Consider the requested action, not only the account type.**

For example, asking which funds to buy inside a Health Savings Account belongs to `investing_retirement`, while asking about HSA eligibility or contribution rules belongs to `financial_services_other`.

**Rule 3: Distinguish immediate budgeting from retirement strategy.**

Questions about current expenses, affordability, and short-term savings allocation belong to `saving_budgeting`.

Questions primarily about retirement contributions, investment allocation, and long-term retirement strategy belong to `investing_retirement`.

**Rule 4: Use `financial_services_other` for a specific administrative or service-related question.**

This label should not automatically be assigned to every post that is difficult to classify. It should be used when the main issue genuinely concerns taxes, insurance, banking procedures, employment benefits, or financial administration.

---

## 6. Final Taxonomy

The four finalized labels for TakeMeter are:

1. `debt_credit`
2. `saving_budgeting`
3. `investing_retirement`
4. `financial_services_other`

Each Reddit post will receive exactly one label based on its primary financial question.

These definitions and decision rules will guide manual annotation, AI-assisted labeling, and model evaluation.

## 7. Next Step

The next milestone is to create `labels.csv` containing 200 unique real Reddit posts with the following columns:

- `text`
- `label`
- `note`

The first 20 posts will be labeled manually without AI-generated suggestions. The remaining posts may receive AI-assisted label suggestions, but each label must be manually reviewed before inclusion in the final dataset.