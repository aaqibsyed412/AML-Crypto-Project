# AML-Crypto-Project
Detecting Illicit Cryptocurrency Transactions & Measuring Model Drift
An end-to-end anti-money-laundering (AML) analysis on 200,000+ real Bitcoin transactions, built to answer three questions a working AML team actually asks: Can we detect illicit activity? Does our detector stay accurate over time? Does it beat the simple rules we already use?
Built in Python (pandas, matplotlib, scikit-learn). The headline result is not just a model — it's an honest evaluation of how that model behaves when deployed against a changing criminal landscape.

The problem
Money laundering through cryptocurrency is a multi-billion-dollar problem, and the data is brutally imbalanced: in this dataset, only ~2% of labeled transactions are illicit. That single fact shapes every decision in this project — a model that flags nothing is 98% "accurate" and catches zero criminals. So accuracy is ignored throughout; recall (the share of real criminals caught) is the metric that matters.
The data
The Elliptic Bitcoin dataset — the largest public labeled crypto-AML dataset:

203,769 transactions, 234,355 payment flows
166 features per transaction, plus a time step (1–49) spaced ~2 weeks apart
Labels: illicit (4,545), licit (42,019), unknown (157,205)

Known limitation, stated up front: the dataset has transactions but not the wallet addresses behind them, so a flagged transaction can't be traced to a specific actor (the "Elliptic++" extension addresses this).

Readme · MDCopyDetecting Illicit Cryptocurrency Transactions & Measuring Model Drift
An end-to-end anti-money-laundering (AML) analysis on 200,000+ real Bitcoin transactions, built to answer three questions a working AML team actually asks: Can we detect illicit activity? Does our detector stay accurate over time? Does it beat the simple rules we already use?
Built in Python (pandas, matplotlib, scikit-learn). The headline result is not just a model — it's an honest evaluation of how that model behaves when deployed against a changing criminal landscape.


The problem
Money laundering through cryptocurrency is a multi-billion-dollar problem, and the data is brutally imbalanced: in this dataset, only ~2% of labeled transactions are illicit. That single fact shapes every decision in this project — a model that flags nothing is 98% "accurate" and catches zero criminals. So accuracy is ignored throughout; recall (the share of real criminals caught) is the metric that matters.
The data
The Elliptic Bitcoin dataset — the largest public labeled crypto-AML dataset:

203,769 transactions, 234,355 payment flows
166 features per transaction, plus a time step (1–49) spaced ~2 weeks apart
Labels: illicit (4,545), licit (42,019), unknown (157,205)



Known limitation, stated up front: the dataset has transactions but not the wallet addresses behind them, so a flagged transaction can't be traced to a specific actor (the "Elliptic++" extension addresses this).
Approach
The project moves through four steps, each answering one question:
1. Detect — Can we predict illicit transactions, and what drives them? A logistic regression classifier, with balanced class weighting and standardized features to handle the 2% imbalance.
2. Drift — Does the detector stay accurate over time? A forward time-split: the model is trained on time steps 1–34 and tested on 35–49, so it must predict the future rather than a random sample.
3. Benchmark — Does it beat a simple existing rule? A champion/challenger comparison — the offline analog of an A/B test — pitting the model against a single-rule baseline (a depth-1 decision tree).
4. Validate — Is the difference real or just luck? McNemar's test for statistical significance.



Key findings
1. The detector works — but the time-aware number is the honest one.
On a random train/test split, the model catches 93% of illicit transactions (recall). But on a forward time-split — the deployment-realistic test, where the model predicts a future period it has never seen — recall drops to 88%.
Recall degrades from 93% → 88% when the model must predict the future rather than a random sample. This is temporal model drift: criminal patterns shift over time (the dataset captures a dark-market shutdown, visible as a collapse in illicit volume after ~time step 43), so a model trained on the past gets blinder on the future. A random split hides this; only a time-split exposes it.
Illicit transaction volume is highly volatile and collapses after ~time step 43 — consistent with a major illicit marketplace shutdown. This instability is what motivates the drift test.
2. The model beats a simple rule — at a cost.
This is a champion/challenger evaluation — the offline equivalent of an A/B test. A live A/B test isn't viable here: you can't randomly route real transactions through a weaker detector without letting money laundering through one arm of the experiment. So both models are run head-to-head on the same held-out time-split data, and the winner is validated statistically (see finding 3).
Results on the time-split test set (illicit class): the single-rule champion catches 76% of criminals at 0.21 precision, while the logistic-regression challenger catches 88% at 0.18 precision.
The model catches 12 points more criminals (76% → 88%) but with slightly lower precision — the classic recall-vs-false-alarm trade-off every AML team must decide on.
3. The difference is statistically significant — and points to a smarter strategy.
McNemar's test: p ≈ 3e-56 (vastly below 0.001) — the performance gap is real, not chance. But the disagreement is bidirectional: the baseline got 2,231 transactions right that the model missed, while the model got 1,292 right that the baseline missed. The two models have different blind spots, which suggests an ensemble or human-in-the-loop routing approach over picking a single model.
4. The real production cost isn't drift — it's false alarms.
Precision held at ~0.18 across every split. So while drift is real, it's modest; the dominant operational challenge is the false-alarm rate (analyst alert fatigue), not temporal decay.
