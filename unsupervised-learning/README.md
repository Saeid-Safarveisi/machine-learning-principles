# Unsupervised Learning

This section contains my notes and practical examples for unsupervised machine learning techniques.

## What is Unsupervised Learning?

Unsupervised learning works with **unlabeled data**.

The main goal is to discover *patterns*, *structures*, or *groups* in the data.

## Main Techniques

- Clustering
- Dimensionality Reduction
- Anomaly Detection

## Simple Python Example

```python
from sklearn.cluster import KMeans

model = KMeans(n_clusters=3, random_state=42)
model.fit(X)
```
