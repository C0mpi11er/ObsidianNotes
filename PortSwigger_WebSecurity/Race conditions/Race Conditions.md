# 🏎️ Race Conditions (RC)

Race conditions are a type of **business logic vulnerability** where a system processes requests concurrently without proper safeguards, leading to unintended behavior.

## 💥 Core Concept

A collision occurs when **multiple distinct threads (requests)** interact with the **same data** at the **same time**. An attacker uses carefully timed requests to force this collision for malicious gain.

* **Race Window:** The brief period of time (often milliseconds) during which a collision is possible (e.g., between a check and an update).

## 💰 Limit Overrun Race Conditions

The most common type, allowing an attacker to **exceed a limit** (e.g., use a single-use coupon multiple times, withdraw more money than available).

**Example: Single-Use Discount**
1.  **Check:** (Is coupon used?)
2.  **Apply Discount**
3.  **Update:** (Mark coupon as used)

If two requests enter at the same time, they both pass the initial check (1) before the database is updated (3), resulting in a double discount. This is a sub-type of **Time-of-Check to Time-of-Use (TOCTOU)** flaw.

## 🛠️ Exploitation Techniques

The main challenge is **aligning the race window**. Tools like **Burp Suite** are used to send requests in parallel:

* **HTTP/1 (Classic):** Last-byte synchronization.
* **HTTP/2 (Modern):** **Single-Packet Attack** - sends 20-30 requests in a single TCP packet to neutralize network jitter and increase the chance of collision.
* **Turbo Intruder:** A Burp Suite extension for more complex attacks (e.g., large number of requests, staggered timing).

## 👻 Advanced RCs: Hidden Multi-Step Sequences

A single request can trigger an entire sequence of operations, transitioning the application through **temporary sub-states**.

**Methodology: Predict, Probe, Prove**
1.  **Predict:** Look for security-critical endpoints that interact with the same record (collision potential).
2.  **Probe:** Send a group of requests **in sequence** (normal behavior) and then **in parallel** (attack). Look for *any* deviation in response (e.g., different status code, different email content).
3.  **Prove:** Replicate the effect and understand the vulnerability to maximize impact.

**Examples of Advanced RCs:**

* **Multi-Endpoint RC:** Timing requests to two different endpoints (e.g., adding items to a basket between payment validation and order confirmation).
* **Single-Endpoint RC:** Sending parallel requests with different values to the *same* endpoint (e.g., using two different usernames in parallel to reset a victim's password).
* **Partial Construction RC:** Exploiting a temporary state where an object (like a new user) exists but a critical security property (like an API key) is **uninitialized**.

## 🛡️ Prevention

The goal is to **eliminate sub-states** on sensitive endpoints.

* **Atomic Operations:** Ensure state changes are **atomic** (uninterruptible) using the datastore's concurrency features (e.g., a **single database transaction** for check and confirmation).
* **Data Integrity:** Use datastore features like **column uniqueness constraints**.
* **Consistency:** Avoid updating session variables individually; use a **batch update** to keep the session internally consistent.