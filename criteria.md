# Acceptance Criteria — TakeMeter (r/soccer comments)

Written before any training run.

Facts these targets rest on: 3 labels (chance = 0.33); 200 rows split 70/15/15 gives 140 train, 30 validation, 30 test; one test prediction is worth 3.3 accuracy points; three seeds (42, 7, 2024), and a target has to hold in every one of them.

## 1. Overall accuracy
On the held-out test set, accuracy is at least 0.65 in each of the three seeds.
**Why:** Criterion 3 caps my largest label at 50%, so always guessing it scores at most 0.50, and random guessing scores 0.33. I want at least 15 points above the worst-case guesser, because a model that only beats the guesser by a few points has not learned the distinction. With 30 test posts, 0.65 means 20 or more correct. I did not pick 0.80 because 140 training examples and an ambiguous analysis/hot_take boundary make that unlikely.

## 2. Per-label performance
Every label has F1 of at least 0.55 on the test set in each seed, except analysis, which needs at least 0.45.
**Why:** analysis is my thinnest label, with about 40 rows, so only about 6 land in the test split. One miss moves its recall by about 17 points, so its target is looser. The other two labels have far more test examples, so 0.55 is a fair floor for a label the model should clearly have learned.

## 3. Balance
In labels.csv, no label is above 50% of the rows and none is below 20%.
**Why:** The brief's cap is 70%, but at 70% a guesser would score 0.70, which makes criterion 1 meaningless. 50% keeps the majority guess under my accuracy target. The 20% floor means at least 40 analysis rows, which is the minimum I think gives the model something to learn and the test split more than a handful of examples.

## 4. Consistency
On the 30 staff posts, labeled under the staff taxonomy and not mine, I agree with staff on at least 23 of 30 (0.75).
**Why:** The staff set measures whether I can apply a written rule the way its authors did, which is the same skill my own 200 labels depended on. Several of the 30 are chosen to be ambiguous, so I do not expect 90%. Below about 70% would mean I apply written rules loosely, and that would weaken my own labels too.

## 5. Confidence
In each seed, accuracy on the model's most-confident third of test predictions is at least 0.20 higher than on its least-confident third.
**Why:** If confidence means something, the model should be right more often when it is sure. Each third is about 10 test posts, so one prediction is worth 10 points. 0.20 means the two groups differ by at least two predictions, which is the smallest gap I would not put down to a coin flip.

<!-- UNIT 6: never edit or delete an original line. If a criterion turns out to be unmeasurable, add the revision underneath it. Lowering a target because it was missed is not a revision. -->
