# K-Means Clustering

K-means is an **unsupervised clustering algorithm** that partitions \(N\) observations into a predefined number of clusters \(K\).

The basic idea is simple:

> Observations assigned to the same cluster should be as similar to each other as possible.

For K-means, similarity is measured using **squared Euclidean distance**.

---

## 1. Cluster Assignment

Each observation belongs to **one and only one cluster**.

The assignment can be represented as:

$$
C(i) = k
$$

where:

* \(i\) represents an observation,
* \(k \in \{1,\dots,K\}\) represents a cluster,
* \(C(i)\) tells us which cluster observation \(i\) belongs to.

Therefore, clustering can be viewed as a **many-to-one mapping**:

$$
\text{Observations} \rightarrow \text{Clusters}
$$

---

## 2. Objective Function

The goal of K-means is to minimize the **total within-cluster sum of squares (WCSS)**:

$$
J(C,\mu)
=
\sum_{k=1}^{K}
\sum_{x_i \in C_k}
\|x_i-\mu_k\|^2
$$

where:

* \(K\) = number of clusters,
* \(C_k\) = observations assigned to cluster \(k\),
* \(x_i\) = observation \(i\),
* \(\mu_k\) = centroid of cluster \(k\).

In simple terms:

> For every cluster, calculate the squared distance between every observation and its centroid, and then add all of these distances together.

K-means tries to find the partition that makes this total value as small as possible.

---

## 3. Why Is the Optimization Difficult?

In principle, we could examine **every possible partition** of the observations into \(K\) clusters and choose the one with the smallest objective value.

The number of possible partitions is finite, but it increases extremely quickly as the number of observations increases.

For example:

$$
S(10,4)=34,105
$$

while:

$$
S(19,4)\approx10^{10}
$$

Therefore, exhaustive search becomes impractical even for relatively small datasets.

K-means instead searches through only a small subset of possible partitions.

---

## 4. Two Unknown Elements

The optimization problem contains two unknowns:

### Cluster assignments

Which cluster should each observation belong to?

$$
C(i)
$$

### Cluster centroids

Where should the center of each cluster be?

$$
\mu_1,\mu_2,\dots,\mu_K
$$

Trying to optimize both simultaneously is difficult.

K-means solves this using **alternating optimization**:

> Fix one → optimize the other → switch → repeat.

---

## 5. K-Means Algorithm

### Step 1 — Initialize Centroids

Start with \(K\) initial centroids:

$$
\mu_1,\mu_2,\dots,\mu_K
$$

---

### Step 2 — Assignment Step

Keep the centroids fixed.

Assign every observation to the closest centroid:

$$
C(i)
=
\arg\min_{1\leq k\leq K}
\|x_i-\mu_k\|^2
$$

Therefore:

$$
\text{Fixed centroids}
\Rightarrow
\text{Best cluster assignments}
$$

---

### Step 3 — Centroid Update

Now keep the cluster assignments fixed.

For each cluster, calculate the mean of its observations:

$$
\mu_k
=
\frac{1}{N_k}
\sum_{x_i\in C_k}x_i
$$

The mean is the point that minimizes the total squared Euclidean distance of the observations within that cluster.

Therefore:

$$
\text{Fixed assignments}
\Rightarrow
\text{Best centroid = mean}
$$

---

### Step 4 — Repeat

Repeat the two steps:

```text
Assign observations
        ↓
Recalculate centroids
        ↓
Assign observations again
        ↓
Recalculate centroids
        ↓
       ...
```

until the cluster assignments stop changing.

---

## 6. Why Does the Objective Decrease?

K-means is an **iterative greedy descent algorithm**.

During the assignment step:

* the centroids are fixed,
* every observation is assigned to its closest centroid,
* therefore the objective cannot increase.

During the centroid-update step:

* the assignments are fixed,
* each centroid is replaced by the cluster mean,
* the mean minimizes the within-cluster squared error,
* therefore the objective again cannot increase.

Hence:

$$
J^{(0)}
\geq
J^{(1)}
\geq
J^{(2)}
\geq
\dots
$$

The objective either **decreases or remains unchanged** after every step.

---

## 7. Convergence vs. Global Optimum

Because each step decreases or preserves the objective, K-means eventually converges.

However:

$$
\boxed{\text{Convergence} \neq \text{Global Optimum}}
$$

K-means may converge to a **local minimum** rather than the globally best partition.

Different initial centroids can therefore lead to different solutions.

```text
Initialization A → Solution A
Initialization B → Solution B
Initialization C → Solution C
```

A practical strategy is to run K-means several times with different initializations and keep the solution with the smallest objective value.

---

## 8. Data Type and Distance

Classical K-means is designed for **quantitative / numerical variables**.

It uses squared Euclidean distance:

$$
d(x_i,x_j)
=
\|x_i-x_j\|^2
$$

The use of squared Euclidean distance is important because it makes the **mean** the optimal cluster representative.

Therefore:

> Classical K-means = numerical data + mean centroid + squared Euclidean distance.

For primarily categorical data, classical K-means is not directly appropriate and related clustering methods should be considered instead.

---

## 9. Mental Model

```text
                     DATA
                       │
                       ▼
                   Choose K
                       │
                       ▼
              Initialize centroids
                       │
                       ▼
        ┌──────────────────────────┐
        │     FIX CENTROIDS        │
        │                          │
        │ Assign each observation │
        │ to its nearest centroid  │
        └─────────────┬────────────┘
                      │
                      ▼
        ┌──────────────────────────┐
        │     FIX ASSIGNMENTS      │
        │                          │
        │ Recalculate each         │
        │ centroid as the mean     │
        └─────────────┬────────────┘
                      │
                      ▼
              Objective decreases
                      │
                      ▼
          Assignments unchanged?
                /             \
              No               Yes
              │                 │
              └── Repeat        ▼
                               STOP
```

---

## 10. Key Points to Remember

| Concept                | Principle                                       |
| ---------------------- | ----------------------------------------------- |
| Type                   | Unsupervised clustering                         |
| Input                  | Quantitative / numerical variables              |
| Number of clusters     | \(K\) is predefined                             |
| Cluster assignment     | Each observation belongs to exactly one cluster |
| Distance               | Squared Euclidean distance                      |
| Cluster representative | Mean / centroid                                 |
| Objective              | Minimize total within-cluster sum of squares    |
| Optimization           | Alternating assignment and centroid update      |
| Behavior               | Objective decreases or remains unchanged        |
| Convergence            | Guaranteed to reach a stable solution           |
| Global optimum         | Not guaranteed                                  |
| Main risk              | Convergence to a local minimum                  |

---

## Summary

> **K-means searches for a partition that minimizes the total within-cluster sum of squared distances. Since both cluster assignments and centroids are unknown, it uses alternating optimization: with centroids fixed, each observation is assigned to its nearest centroid; with assignments fixed, each centroid is replaced by the mean of its cluster. Each step decreases or preserves the objective, so the algorithm converges, although the final solution may be a local rather than global minimum.**

## Reference

This summary is based on the K-means and combinatorial clustering discussion from the supplied statistical learning material, especially the treatment of within-cluster scatter, iterative greedy descent, and alternating optimization.
