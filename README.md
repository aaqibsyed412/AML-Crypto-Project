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
