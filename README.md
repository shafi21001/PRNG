# 🔐 Custom PRNG with OS Entropy Seeding

This project demonstrates a lightweight **custom pseudorandom number generator (PRNG)** implemented in Python. The randomness is **securely seeded from the operating system’s entropy pool**, and its quality is analyzed using a **visual 2D scatter plot**.

> 📓 Built for cryptography education, randomness research, and PRNG experimentation.

---

## 🧠 What Is Entropy?

Imagine your computer’s operating system has a special **“randomness bucket”** 🧳, known as the **entropy pool**. It collects **unpredictable data** from various real-world sources to ensure secure randomness.

* **High Entropy**: Hard-to-guess events (e.g., exact timing between keystrokes)
* **Low Entropy**: Predictable events (e.g., typing common words like “the”)

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

This custom PRNG is built using a mix of bitwise operations and cryptographic hashing. Here’s how it works:

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

## 📊 Visual Distribution Analysis

We analyze the quality of the PRNG output with a **2D scatter plot**:

### Scatter Plot of (xᵢ, xᵢ₊₁)

```python
x_vals = numbers[:-1]
y_vals = numbers[1:]
plt.scatter(x_vals, y_vals)
```

| Feature        | What to Look For                                             |
| -------------- | ------------------------------------------------------------ |
| **Uniformity** | Even spread of points across 0–100 range                     |
| **Dependency** | Lack of visible lines or patterns between consecutive values |

If the plot looks like scattered stars 🌌 with no structure — your PRNG is behaving randomly!

---

## 📁 Files Included

```
🔹 prng.ipynb       # Jupyter Notebook with PRNG implementation + visualization
🔹 README.md        # Project documentation (this file)
```

---

## 📦 Requirements

Install required packages (if using locally):

```bash
pip install matplotlib seaborn numpy
```

---

## 🚀 How to Run

Use `prng.ipynb` in:

* ✅ Jupyter Notebook (locally)
* ☁️ Google Colab (online, no setup required)

Just open the notebook and run all cells.

---

## 🔍 Use Cases

* Teaching how randomness is generated from entropy
* Analyzing pseudorandom number quality visually
* Building your own secure PRNG model
