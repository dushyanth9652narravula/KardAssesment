# Kard Take-home Interview
```
                          #############################
         %#############################################################
      #####################################################################
    #########################################################################
   ###########################/###############################################
  ########################          ###########################################
 #######################      (      ###########################################
 #######################     ###/    ###########################################
 #######################     ###/    ###########################################
 #######################     ###/    #############(      #######################
 #######################     ###/    ##########/           ,####################
 #######################     ###/    ########       ###     ####################
 #######################     ###/    #####       #####/     ####################
 #######################     ###/  ####,      ,#####       #####################
 #######################     ###/####       #####       ########################
 #######################     #######     #####       *##########################
 #######################     #####      ####       #############################
 #######################     ####,     ####(     ###############################
 #######################     ####     #######      #############################
 #######################     ####    *### #####      ###########################
 #######################     ####    ####   #####      #########################
 #######################     ####    ####     #####      #######################
 #######################     ####    #####      #####      #####################
 #######################     ####    (######      #####     ####################
 #######################     ####    (########      ###*    .###################
 ########################            ###########            ####################
 ##########################        ################      #######################
 %#############################################################################
  #############################################################################
   ###########################################################################
     #######################################################################
       &#################################################################
             ######################################################%
```

Hi! Thanks for applying to our Senior/Staff Machine Learning Engineer position
at Kard. We hope you enjoy the take-home part of our interview process.

Please spend about **one hour** on this. We mean it: we grade with the time
limit in mind, and we'd rather see a clear one-hour proof of concept than a
polished four-hour one. If you run out of time, tell us what you'd do next and
how you'd validate it.

What we're looking for is clear thinking, clear code, and good communication.

## The Problem

Kard receives cardholder transactions from our credit card issuer partners. We
aim to match these transactions to the name of the merchant where they
occurred. Each transaction comes with a description value which often includes
this merchant name, but there's much variation in the description, and terms
are often misspelled or re-ordered.

Today we match with regular expressions on the description field, combined
with a mapping that stores past matches. That needs a hand-written rule for
every merchant, so it's manual, and the rules miss variants they weren't
written for.

Please build a proof-of-concept classifier that uses machine learning to match
each transaction to one of these five merchant brands, or to `NONE`:

| Label       | Includes                                                           |
|-------------|--------------------------------------------------------------------|
| `Target`    | Target stores and target.com                                       |
| `Walmart`   | Walmart stores, Supercenters, and walmart.com (not Sam's Club)     |
| `Amazon`    | amazon.com, Marketplace, Prime, digital, Fresh (not Whole Foods)   |
| `Starbucks` | Starbucks stores and card/app reloads                              |
| `McDonalds` | McDonald's restaurants                                             |
| `NONE`      | Everything else                                                    |

Match to the brand, not the specific location. Use the description field. A purchase of one of these brands made through a delivery app,
buy-now-pay-later service, or payment app (DoorDash, Klarna, PayPal, Venmo)
still counts as that brand.

### Your goal

**For each merchant, maximize recall while keeping precision at or above 98%.**
Calling a small merchant "Target" when it isn't is much worse than missing a
real Target transaction.

### What a good outcome looks like

Target is hard because its brand is a single common word.

Good matches:

- `TARGET.COM * 800-591-3869 MNUS` -> `Target`
- `TARGET 00028506 BROOKLYN NYUS` -> `Target`
- `TARGET T-2764 NEWARK DE` -> `Target`

Must NOT match Target (all small merchants, so `NONE` is correct):

- `TARGET LOTTO COLUMBUS GAUS`
- `TARGETMASTER INDOOR SH`
- `TARGET LIQUOR INC. LITTLE ROCK ARUS`
- `WELLONTARGET FITNESS`

## The Data

The data is real. We've replaced identifiers and kept only the description, which is noisy
and full of transactions we don't care about (CashApp, transfers, etc.). Everything runs fine on a laptop; no cloud setup needed.

| File                          | Rows   | Labels                  | Purpose                        |
|-------------------------------|--------|-------------------------|--------------------------------|
| `data/transactions_sample.csv` | ~160k | `weak_label` (noisy)   | Training                       |
| `data/dev_labeled.csv`        | ~500   | `weak_label`, `label`   | Measuring, tuning, error analysis |
| `data/test_unlabeled.csv`     | ~1,000 | none                    | Predict these; we score them   |

**`weak_label`** comes from the rules we run in production today. It's in the
training and dev files. It's noisy: it misses descriptor variants the rules
weren't written for, and it can be fooled by look-alikes. Use it however you
like: as training labels or as a baseline. The question we care
most about is whether your approach does better than the rules, and where.

**`label`** is our reference label, and only the dev set has it. The dev set is
deliberately enriched with hard cases like the ones above, so its class balance
doesn't match real traffic. Use it to measure, set thresholds, and study
errors. If you train on any of it, tell us how you kept your numbers honest.

### Columns

- **row_id** - row identifier
- **description** - transaction description
- **weak_label** - label from our current production rules (train and dev only)
- **label** - reference label, dev set only

## Using AI

We encourage you to use AI tools: coding assistants, chat models, LLMs inside
your pipeline, whatever you'd actually use on the job. We use them every day,
and we're interested in how you work with them. Tell us about it in your
write-up (below). We're not grading prompt craft; we want to see your workflow.

## What to Send Us

1. **Your code.** We should be able to run it.
2. **`test_predictions.csv`** with two columns, `row_id` and
   `predicted_merchant` (one of the six labels above), one row per row in
   `test_unlabeled.csv`.
3. **`WRITEUP.md`**, one page max, written for a technically knowledgeable
   leader:
   - Your approach, and why you chose it over the alternatives
   - Precision and recall per merchant on the dev set, for your model and for
     `weak_label`
   - The two or three biggest weaknesses a skeptical peer would point out, and
     how you'd address them
   - How you'd extend this to the next 500 merchants without hand-labeling
     each one
   - **How you used AI**, in a few bullets: which tools and for what, what you
     handed off and what you kept, at least one place it was wrong or
     unhelpful and how you caught it, and how you checked its output. If it's
     easy, include a few key prompts or an excerpt of a session log.

## Returning Your Work

Please do not include the data files in your response.

If you made a folder of work, zip it up and return it via the link you
received from Kard.

If you used a git repository, with a `git init` in a `main` branch and
incremental commits, commit your final work and run:

```bash
git bundle create [YourName].bundle main
```

For instance, if your name were Pat Patterson, run `git bundle create
PatPatterson.bundle main`. Then send us the `.bundle` file.

## After the Takehome

If we talk further, we'd like to walk through your submission, including how
you worked with AI on it, and explore the following with you live:

- When the source datasets are huge, what optimization techniques would you
  use?
- When the source data is a stream and a downstream process consumes this
  model's output in near real time, how would you architect it?
- How would you make sure your deployment and serving are resilient? Have you
  done this in production before?
- How would you make sure your model stays valid and robust (not over-fit,
  adaptive to new scenarios, resistant to manipulation), initially and over
  time? Have you done this in production before?
- How would you do all of the above cost-effectively? Have you done this
  before, in development and production?

## Thank You

Thank you for talking with us and taking our take-home! We appreciate your
time and interest.
