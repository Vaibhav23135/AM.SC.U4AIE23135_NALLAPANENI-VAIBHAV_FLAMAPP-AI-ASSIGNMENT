# FlamApp AI – Research & Development Assignment

## Candidate Information

| Field | Details |
|------|---------|
| **Name** | Nallapaneni Vaibhav |
| **Course** | B.Tech Computer Science and Engineering (Artificial Intelligence) |
| **ROLL No** | AM.SC.U4AIE23135 |
| **College** | Amrita Vishwa Vidyapeetham |
| **Assignment** | Research & Development |

---

# Problem Statement

The objective of this assignment is to estimate the unknown parameters of the following parametric curve using the dataset in `xy_data.csv`.

The curve is defined by:

- $x = t\cos(\theta) - e^{M|t|}\sin(0.3t)\sin(\theta) + X$
- $y = 42 + t\sin(\theta) + e^{M|t|}\sin(0.3t)\cos(\theta)$

The unknown parameters to be estimated are:

- **θ (Theta)**
- **M**
- **X**

Subject to the following constraints:

| Parameter | Range |
|-----------|---------|
| θ | $0^\circ < \theta < 50^\circ$ |
| M | $-0.05 < M < 0.05$ |
| X | $0 < X < 100$ |
| t | $6 < t < 60$ |

The goal is to recover parameter values that best match the supplied sample points while minimizing the L1 distance between the predicted and observed coordinates.

---

# Mathematical Formulation

The model is expressed as:

- $x = t\cos(\theta) - e^{M|t|}\sin(0.3t)\sin(\theta) + X$
- $y = 42 + t\sin(\theta) + e^{M|t|}\sin(0.3t)\cos(\theta)$

For each candidate parameter combination, the value of $t$ must first be computed before reconstructing the curve.

---

# Derivation of $t$

Since the dataset contains only the coordinates $(x, y)$, the parameter $t$ is unknown.

To estimate it, the exponential perturbation term is eliminated by projecting each point onto the rotated coordinate axis.

Starting from:

- $x = X + t\cos\theta - e^{M|t|}\sin(0.3t)\sin\theta$
- $y = 42 + t\sin\theta + e^{M|t|}\sin(0.3t)\cos\theta$

Subtracting the constant offsets gives:

- $x - X = t\cos\theta - e^{M|t|}\sin(0.3t)\sin\theta$
- $y - 42 = t\sin\theta + e^{M|t|}\sin(0.3t)\cos\theta$

Multiplying the first equation by $\cos\theta$ and the second by $\sin\theta$, then adding them, yields:

- $(x - X)\cos\theta + (y - 42)\sin\theta = t(\cos^2\theta + \sin^2\theta)$

Using the identity $\cos^2\theta + \sin^2\theta = 1$, we obtain:

- $t = (x - X)\cos\theta + (y - 42)\sin\theta$

This expression allows direct computation of $t$ for each candidate pair $(\theta, X)$, avoiding iterative estimation.

---

# Approach

The overall solution follows an exhaustive search strategy over the unknown parameters.

### Step 1

Load the provided dataset.

```python
df = pd.read_csv("xy_data.csv")
```

---

### Step 2

Generate candidate values for each unknown parameter.

| Parameter | Search Space |
|-----------|-------------|
| θ | 0° – 50° |
| M | -0.05 – 0.05 |
| X | 0 – 100 |

---

### Step 3

For each candidate pair $(\theta, X)$, compute:

- $t = (x - X)\cos\theta + (y - 42)\sin\theta$

Only values satisfying $6 < t < 60$ are retained.

---

### Step 4

For each candidate value of **M**, reconstruct the curve using:

- $x = t\cos\theta - e^{M|t|}\sin(0.3t)\sin\theta + X$
- $y = 42 + t\sin\theta + e^{M|t|}\sin(0.3t)\cos\theta$

---

### Step 5

Compute the total L1 distance:

Error = Σ|x_pred - x| + Σ|y_pred - y|

The parameter combination producing the minimum L1 distance is selected as the optimal solution.

To improve computational efficiency, NumPy vectorization was used to evaluate all candidate values of **M** simultaneously instead of iterating through them individually.

---

# Experimental Results

# Parameter Estimation with Generalization

Although not required in the assignment, an additional validation experiment was performed to evaluate how well the estimated parameters generalize to unseen data.

The dataset was divided into two subsets.

- **Training Samples : 1200**
- **Testing Samples : 300**

The unknown parameters were estimated **only using the first 1200 samples**.

The obtained parameters were then directly applied to reconstruct the remaining **300 unseen samples** without any additional optimization.

**Notebook**

```
solution_with_generalization.ipynb
```



### Training Result

```text


Theta (deg) : 30
Theta (rad) : 0.523599
M           : 0.030
X           : 55
Error        : 0.025024
```

---

## Generalization Test

The learned parameters were evaluated on the remaining **300 unseen samples**.

```text


Samples Used      : 300
Total L1 Error    : 0.005810
Average L1 Error  : 0.000019
```

The extremely small average L1 error indicates that the estimated parameters successfully generalize to unseen data instead of merely fitting the training samples.

---

# Experimental Summary

| Experiment | Training Samples | Test Samples | θ | M | X | Training Error | Test Error |
|------------|----------------:|-------------:|---:|---:|---:|--------------:|-----------:|
| Generalization | 1200 | 300 | 30° | 0.030 | 55 | 0.025024 | 0.005810 |


---

# Final Estimated Parameters

The optimization consistently converged to the following parameter values.

| Parameter | Estimated Value |
|-----------|----------------:|
| **θ** | **30°** |
| **θ (radians)** | **0.523599** |
| **M** | **0.030** |
| **X** | **55** |

These parameters produced the minimum L1 error while satisfying all constraints specified in the assignment.

---

## References

- Assignment Description: **AI_R&D Assignment.pdf**
- Dataset: **xy_data.csv**
- Interactive Curve Visualization: https://www.desmos.com/calculator/rfj91yrxob