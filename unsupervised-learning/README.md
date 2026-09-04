# K-Means Clustering

K-means is an **unsupervised clustering algorithm** used to divide observations into a predefined number of clusters, `K`, so that observations belonging to the same cluster are as similar as possible.

For classical K-means:

* the features are **quantitative / numerical**;
* similarity is represented through **squared Euclidean distance**;
* each observation belongs to **one and only one cluster**;
* each cluster is represented by its **mean (centroid)**.

---

## 1. Cluster Assignment as a Many-to-One Mapping

Suppose we have `N` observations and `K` clusters, where `K < N`.

The cluster assignment can be represented as:

```math
C(i) = k
```

where `C(i)` tells us that observation `i` belongs to cluster `k`.

Therefore:

```math
C:\{1,\ldots,N\}\rightarrow\{1,\ldots,K\}
```

This is a **many-to-one mapping**:

* every observation is assigned to exactly one cluster;
* many different observations can be assigned to the same cluster.

K-means therefore produces a **hard clustering**: an observation belongs to one cluster rather than having partial membership in several clusters.

---

## 2. How Is Similarity Defined?

K-means needs a way to determine whether observations are close to one another.

For classical K-means, the dissimilarity measure is **squared Euclidean distance**:

```math
d(x_i,x_j)=\|x_i-x_j\|_2^2
```

A smaller distance means that two observations are more similar in the feature space.

This choice is important because squared Euclidean distance is directly connected to the use of the **mean** as the cluster representative.

Therefore, the classical K-means setting can be summarized as:

> **Numerical features + squared Euclidean distance + mean centroid**

---

## 3. What Does K-Means Try to Optimize?

For each cluster `k`, let:

* `C_k` be the observations assigned to that cluster;
* `\mu_k` be its centroid.

K-means minimizes the **total within-cluster sum of squares (WCSS)**:

```math
J(C,\mu)
=
\sum_{k=1}^{K}
\sum_{x_i\in C_k}
\|x_i-\mu_k\|_2^2
```

Read this objective as:

> For every cluster → for every observation in that cluster → calculate its squared distance to the cluster centroid → add all distances together.

The desired partition is therefore the one that makes:

```math
\text{total within-cluster variation}
```

as small as possible.

Equivalently:

```math
\text{Good clustering}
\Longrightarrow
\text{observations are close to their own centroids}
```

---

## 4. Why Is Finding the Best Partition Difficult?

In principle, we could:

1. generate every possible assignment of `N` observations to `K` clusters;
2. calculate the objective for each partition;
3. choose the partition with the smallest objective.

The search space is **finite**, because there are only finitely many ways to assign a finite number of observations to `K` clusters.

However, the number of possible partitions grows extremely quickly.

For example:

```math
S(10,4)=34,105
```

but:

```math
S(19,4)\approx10^{10}
```

Therefore:

> **The global search space is finite, but exhaustive search is computationally impractical.**

K-means avoids checking every possible partition and instead follows an iterative path through only a very small subset of them.

---

## 5. The Two Unknowns

The objective contains two things that we do not know.

### A. Cluster Assignments

```math
C(i)
```

Which cluster should each observation belong to?

### B. Cluster Centroids

```math
\mu_1,\mu_2,\ldots,\mu_K
```

Where should each cluster center be located?

Trying to find the optimal assignments and optimal centroids simultaneously is difficult.

K-means deals with this through **alternating optimization**:

> Fix one → optimize the other → switch → repeat.

---

## 6. Initialization

Before alternating optimization can begin, K-means needs an initial set of `K` centroids:

```math
\mu_1^{(0)},\mu_2^{(0)},\ldots,\mu_K^{(0)}
```

A simple initialization is to select `K` observations from the dataset as the initial centroids.

A commonly preferred approach is **K-means++**, which tries to choose better-separated initial centers rather than selecting them completely arbitrarily.

Initialization matters because different starting centroids can lead K-means to different final solutions.

For this reason, K-means can also be run several times using different initializations, keeping the solution with the smallest final WCSS.

---

## 7. Alternating Optimization

Once the initial centroids are available, K-means alternates between two simple optimization problems.

### Step 1 — Fix the Centroids, Optimize the Assignments

For each observation, calculate its distance from every centroid and assign it to the closest one:

```math
C(i)
=
\underset{1\leq k\leq K}{\arg\min}
\;
\|x_i-\mu_k\|_2^2
```

Therefore:

```math
\text{Fixed centroids}
\Longrightarrow
\text{nearest-centroid assignment}
```

This gives the best cluster assignments **for the current centroids**.

---

### Step 2 — Fix the Assignments, Optimize the Centroids

Now the cluster memberships are known.

For each cluster, calculate the mean of its observations:

```math
\mu_k
=
\frac{1}{N_k}
\sum_{x_i\in C_k}x_i
```

The mean is important because:

```math
\mu_k
=
\underset{m}{\arg\min}
\sum_{x_i\in C_k}
\|x_i-m\|_2^2
```

In other words:

> For fixed cluster membership, the arithmetic mean is the point that minimizes the **total squared Euclidean distance** from the observations in that cluster.

Therefore:

```math
\text{Fixed assignments}
\Longrightarrow
\text{best centroid = cluster mean}
```

---

## 8. Why Does the Objective Keep Decreasing?

This alternating procedure gives K-means its **greedy descent** behavior.

### Assignment step

The centroids are fixed and every observation is assigned to its closest centroid.

Therefore the objective cannot increase.

### Centroid-update step

The assignments are fixed and every centroid is replaced by the mean, which minimizes the within-cluster squared error.

Therefore the objective again cannot increase.

Consequently:

```math
J^{(0)}
\geq
J^{(1)}
\geq
J^{(2)}
\geq
\cdots
```

At every iteration, the objective either:

* decreases, or
* remains unchanged.

---

## 9. Why Does K-Means Converge?

Two important facts come together:

### 1. The objective never increases

```math
J^{(t+1)}\leq J^{(t)}
```

K-means continually moves toward an equal or better solution.

### 2. The number of possible cluster assignments is finite

Although the number can be extremely large, there are still only finitely many possible assignments of `N` observations to `K` clusters.

Once an assignment is fixed, its optimal centroids are determined by the corresponding cluster means.

Therefore K-means cannot continue producing strictly better assignments indefinitely.

Eventually it reaches a state where the assignments no longer change:

```text
Assignments
    ↓
Centroids
    ↓
Assignments
    ↓
Centroids
    ↓
...
    ↓
No assignment changes
    ↓
STOP
```

This is the convergence criterion in the classical description of K-means.

---

## 10. Convergence Does Not Mean Global Optimum

This is one of the most important properties of K-means:

```math
\text{Convergence}
\neq
\text{Global optimum}
```

K-means performs a **greedy local search**.

At every step it improves the current solution, but it does not examine all possible cluster assignments.

Therefore it may reach a partition for which no further improvement is found along its current path, while another partition elsewhere in the search space has a smaller objective.

Different initializations may therefore produce:

```text
Initialization A → Local solution A

Initialization B → Local solution B

Initialization C → Better solution
```

This is why initialization matters and why trying multiple starting points can be useful.

---

## 11. Complete Mental Model

```text
                         DATA
                           │
                           ▼
                    Choose number K
                           │
                           ▼
              Define squared Euclidean
                    dissimilarity
                           │
                           ▼
                 Initialize K centroids
                           │
                           ▼
             ┌─────────────────────────┐
             │     FIX CENTROIDS       │
             │                         │
             │ Assign every point to  │
             │ its nearest centroid   │
             └────────────┬────────────┘
                          │
                          ▼
             ┌─────────────────────────┐
             │     FIX ASSIGNMENTS     │
             │                         │
             │ Replace each centroid  │
             │ by the cluster mean    │
             └────────────┬────────────┘
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
                                    │
                                    ▼
                           Local solution
```

---

## 12. Data-Type Consideration

Classical K-means is intended for **quantitative variables** because its centroid is an arithmetic mean and its objective uses squared Euclidean distance.

For categorical variables, simply replacing numbers with arbitrary numeric codes and applying classical K-means is generally not appropriate.

Related algorithms exist for other types of data:

| Data Type                     | Typical Method | Cluster Representative |
| ----------------------------- | -------------- | ---------------------- |
| Numerical                     | K-means        | Mean                   |
| Categorical                   | K-modes        | Mode                   |
| Mixed numerical + categorical | K-prototypes   | Mean + mode            |

Therefore, **K-modes should be viewed as a related extension, not as classical K-means with one small setting changed.**

---

## 13. Practical Points to Remember

### Number of clusters

`K` must be specified before fitting K-means.

Methods such as the **elbow method** or **silhouette analysis**, together with domain knowledge, can help investigate reasonable values of `K`.

### Initialization

Common choices include:

* random initialization;
* K-means++.

Different starting points can produce different solutions.

### Feature scaling

Because K-means is distance-based, features with much larger numerical scales can dominate the Euclidean distance.

Scaling is therefore usually important when features use different units or ranges.

### Outliers

Squared Euclidean distance gives large distances particularly strong influence, making K-means sensitive to extreme observations.

---

## 14. K-Means in One Paragraph

> **K-means partitions numerical observations into `K` clusters by minimizing the total within-cluster sum of squared Euclidean distances. Each observation is mapped to one and only one cluster, producing a many-to-one assignment from observations to clusters. Because both the assignments and centroids are unknown, K-means uses alternating optimization: with centroids fixed, each observation is assigned to its nearest centroid; with assignments fixed, each centroid becomes the mean of its cluster. Both steps decrease or preserve the objective. Since there are finitely many possible assignments, the algorithm eventually reaches a stable partition. However, because it searches only a small part of the enormous assignment space through greedy descent, the resulting solution can be a local rather than global optimum.**

---

## References

* *The Elements of Statistical Learning* — Chapter 14, Unsupervised Learning
* Scikit-learn documentation — `KMeans`
