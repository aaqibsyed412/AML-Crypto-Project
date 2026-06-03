# AML-Crypto-Project

Detecting Illicit Crypto Transactions & Measuring Model Drift


An anti-money-laundering analysis on 200,000+ real Bitcoin transactions (Elliptic dataset), built in Python to answer three questions a real AML team asks: Can we detect illicit activity? Does the detector stay accurate over time? 
Does it beat the simple rules we already use?
Only ~2% of transactions are illicit, so accuracy is meaningless here a model flagging nothing scores 98%. Recall (share of criminals caught) is the metric that matters.
What I found

A logistic-regression detector catches 93% of illicit transactions on a random split but only 88% on a forward time-split (training on the past, testing on the future). That 5-point drop is temporal model drift: criminal patterns shift over time (note the collapse after ~time step 43, a market shutdown), so a model decays in deployment unless retrained.
It beats a simple one-rule baseline in a champion/challenger test (the offline analog of an A/B test): recall 76% → 88%, at a small precision cost — the recall-vs-false-alarm trade-off every AML team faces.
The difference is statistically significant (McNemar's test, p ≈ 3e-56). But the two models miss different transactions, which points to an ensemble or human-in-the-loop approach rather than picking one.
The real production cost isn't drift — it's false alarms. Precision held at ~0.18 throughout, meaning analyst alert-fatigue, not decay, is the dominant operational challenge.
