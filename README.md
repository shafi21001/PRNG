# 🔐 Custom PRNG with Distribution Visualizations

This project demonstrates a powerful **custom pseudorandom number generator (PRNG)** implemented in Python with advanced distribution analysis capabilities. The randomness is **securely seeded from the operating system's entropy pool**, and its quality is analyzed using **multiple visualization techniques** including uniform and non-uniform distribution comparisons.

> 📓 Built for cryptography education, randomness research, statistical analysis, and PRNG experimentation.

---

## 🧠 What Is Entropy?

Imagine your computer's operating system has a special **"randomness bucket"** 🧳, known as the **entropy pool**. It collects **unpredictable data** from various real-world sources to ensure secure randomness.

* **High Entropy**: Hard-to-guess events (e.g., exact timing between keystrokes)
* **Low Entropy**: Predictable events (e.g., typing common words like "the")

### ⚙️ How the OS Fills the Bucket

Your OS watches unpredictable system events like:

* ⌨️ **Keyboard timings**
* 🖱️ **Mouse movements**
* 📀 **Disk activity**
* 🌐 **Network packet arrivals**

It mixes all of these into a high-quality **entropy pool**.

---

## 🧰 `/dev/random` vs `/dev/urandom`

In Linux/Unix systems, programs access randomness through two special devices:

| Device         | Behavior                                                        |
| -------------- | --------------------------------------------------------------- |
| `/dev/random`  | **Blocks** until enough true entropy is available               |
| `/dev/urandom` | Uses the entropy pool + cryptographic PRNG (fast, non-blocking) |

> ✅ Our PRNG uses `os.urandom(32)`, which securely accesses `/dev/urandom` (or Windows Crypto API).

---

## 🧪 How the Custom PRNG Works (Step-by-Step)

This custom PRNG is built using a mix of bitwise operations and cryptographic hashing, with advanced distribution generation capabilities:

### 1. 🔑 Seed Initialization

```python
entropy = os.urandom(32)
hashed = hashlib.sha256(entropy).hexdigest()
self.state = int(hashed, 16) & 0xFFFFFFFF
```

* OS provides **32 bytes of entropy**
* SHA-256 hash spreads entropy uniformly
* Final seed is reduced to a **32-bit state**

### 2. 🔄 State Mixing (Bitwise Scrambling)

```python
self.state = (self.state ^ (self.state << 13)) & 0xFFFFFFFF
self.state = (self.state ^ (self.state >> 17)) & 0xFFFFFFFF
self.state = (self.state ^ (self.state << 5)) & 0xFFFFFFFF
```

* XOR and bit shifting make output highly sensitive to seed and counter

### 3. 🔁 Digest + Counter Mixing

```python
digest = hashlib.sha256(str(self.state + self.counter).encode()).hexdigest()
```

* Adds a counter for extra uniqueness
* Uses **SHA-256 again** to generate more unpredictable bits

### 4. 🎯 Normalize Output to Range \[0, 100]

```python
return int(digest, 16) % 101
```

* Converts digest to integer and reduces it to a number between **0–100**

---

## 🎲 Distribution Generation Features

The custom PRNG supports generating different types of distributions:

### 📊 **Uniform Distribution**
```python
uniform_x, uniform_y = prng.generate_uniform()
```
- Even spread across the entire range [0, 1]
- Perfect for general randomness applications
- Ideal baseline for randomness quality testing

### 🎯 **Beta-like Distribution**
```python
beta_x, beta_y = prng.generate_beta_like()
```
- Clusters around center (0.5) similar to Beta(2,2)
- Uses transformation techniques on uniform output
- Demonstrates distribution shaping capabilities

### 📈 Comprehensive Visualization Suite

#### 1. **2D Scatter Plots**
- Side-by-side comparison of uniform vs. non-uniform distributions
- Visual quality assessment of randomness patterns
- Color-coded for easy distinction

#### 2. **Distribution Histograms**
- Four separate histograms showing X and Y distributions
- Clear visualization of distribution shapes
- Frequency analysis for statistical validation

#### 3. **Statistical Comparison**
- Mean and standard deviation calculations
- Comparison with NumPy's built-in generators
- Quality metrics for randomness assessment

---

## 📊 Visual Distribution Analysis

We analyze the quality of the PRNG output with **multiple visualization techniques**:

### 🌟 Scatter Plot Analysis

```python
plt.scatter(uniform_x, uniform_y, alpha=0.6, s=20, color='blue')
plt.scatter(beta_x, beta_y, alpha=0.6, s=20, color='red')
```

| Distribution Type | What to Look For                                             |
| ----------------- | ------------------------------------------------------------ |
| **Uniform**       | Even spread of points across entire [0,1] × [0,1] space    |
| **Beta-like**     | Clustering around center (0.5, 0.5) with sparse edges     |

### 📈 Histogram Analysis

| Feature        | Uniform Distribution | Beta-like Distribution |
| -------------- | -------------------- | ---------------------- |
| **Shape**      | Flat/rectangular     | Bell-shaped/peaked     |
| **Peak**       | No clear peak        | Peak around 0.5        |
| **Spread**     | Even across range    | Concentrated in center |

### 🧪 Statistical Quality Tests

#### Chi-square Test for Uniformity
```python
chi2_stat, p_value = chisquare(uniform_hist, expected_freq)
```
- **P-value > 0.05**: Good uniformity ✅
- **P-value ≤ 0.05**: Poor uniformity ❌

---

## 📁 Files Included

```
🔹 custom_prng.py               # Custom PRNG implementation with visualizations
🔹 README.md                    # Project documentation (this file)
🔹 custom_prng_scatter.pdf      # Generated scatter plot visualizations
🔹 custom_prng_histograms.pdf   # Generated histogram visualizations
```

---

## 📦 Requirements

Install required packages:

```bash
pip install matplotlib seaborn numpy scipy
```

**Dependencies:**
- `matplotlib` - Plotting and visualization
- `seaborn` - Statistical plots
- `numpy` - Numerical operations
- `scipy` - Statistical tests
- `hashlib` - Cryptographic hashing
- `os` - Operating system entropy access

---

## 🚀 How to Run

### Option 1: Direct Python Execution
```bash
python custom_prng.py
```

### Option 2: Jupyter Notebook
```python
# Copy the code into a Jupyter notebook cell and run
# Ideal for interactive experimentation
```

### Option 3: Google Colab
- Upload the script to Google Colab
- Run all cells for instant visualization
- No local setup required

---

## 📊 Output Examples

### 📈 Generated Visualizations

1. **Scatter Plots**: `custom_prng_scatter.pdf`
   - Left: Uniform distribution (blue points)
   - Right: Beta-like distribution (red points)

2. **Histograms**: `custom_prng_histograms.pdf`
   - Four subplots showing X and Y distributions
   - Clear comparison between distribution types

### 💻 Console Output
```
=== Custom PRNG Distribution Comparison ===
Uniform - Mean X: 0.503, Std X: 0.289
Beta-like - Mean X: 0.498, Std X: 0.201

=== Uniformity Test (Chi-square) ===
Chi-square statistic: 8.432
P-value: 0.492
Uniform quality: Good (p > 0.05 indicates good uniformity)
```

---
