## 🔥 Multi-Endpoint Race Conditions

### 📌 Core Idea

- Involves **sending requests to multiple endpoints simultaneously**
- Exploits **shared state transitions across endpoints**
- Goal: **interfere with workflow logic during a race window**

> Happens when multiple endpoints interact with the same data at the same time

---

### ⚡ Typical Exploit Pattern

- Example flow:
    1. Add item to cart
    2. Pay for item
    3. Order confirmation happens
- Attack:
    - Send:
        - `/checkout`
        - `/add-item`
    - **At the same time**
    - → Modify order **after payment validation but before confirmation**

✅ Result:

- Free items
- Underpriced purchases
- Logic bypass

---

### 🧩 Race Window Problem

- Hard part: **aligning execution timing**
- Even if sent simultaneously:
    - Network delays
    - Backend processing differences  
        → desync requests

---

### 🛠️ Fixing Timing Issues

#### 1. Connection Warming

- Send dummy requests first
- Purpose:
    - Remove connection setup delays
- Makes timing more consistent

---

#### 2. Endpoint Processing Awareness

- Different endpoints = different execution speeds
- Strategy:
    - Slow down fast endpoints
    - Or speed up slow ones (retry / optimize payloads)

---

### 🎯 Abuse Targets

- Payment workflows
- Cart systems
- Account updates
- Resource allocation endpoints

---

## ⚔️ Single-Endpoint Race Conditions

### 📌 Core Idea

- Same endpoint
- Multiple concurrent requests with **different inputs**

---

### 💥 Example: Password Reset

Send simultaneously:

POST /reset-password (user=attacker)  
POST /reset-password (user=victim)

Possible result:

- Session stores:
    - victim user
    - attacker-controlled token

✅ Outcome:

- Account takeover

---

### 🎯 Good Targets

- Email confirmations
- Password resets
- Token-based flows

> Emails often sent asynchronously → easier race window

---

## 🔒 Session-Based Locking

### 📌 Behavior

- Some frameworks (e.g., PHP):
    - Process **one request per session at a time**

---

### 🚨 Problem

- Masks race conditions → false negative

---

### 🛠️ Bypass

- Use:
    - Multiple session tokens
    - Multiple accounts

---

## 🧱 Partial Construction Race Conditions

### 📌 Core Idea

- Objects created in **multiple steps**
- Temporary insecure state exists

---

### 💥 Example

User creation:

1. Create user
2. Assign API key

Between steps:

- User exists
- API key = NULL

---

### ⚔️ Exploit

Send request during gap:

GET /api/user?user=victim&api-key[]=

✅ Trick:

- Abuse **uninitialized values**
- Exploit null/empty comparisons

---

### 🧠 Key Insight

- Look for:
    - `null`
    - empty strings
    - uninitialized fields

---

## ⏱️ Time-Sensitive Attacks

### 📌 Not always “true” race conditions

- But same technique (precise timing)

---

### 💥 Example: Weak Tokens

- Token = timestamp-based
- Trigger:
    - Two resets at same millisecond

✅ Result:

- Same token for multiple users

---

## 🧪 Methodology (Practical Flow)

### 1. Predict

- Focus on:
    - Critical endpoints
    - Shared resources
    - State-changing operations

---

### 2. Probe

- Send:
    - Sequential requests → baseline
    - Parallel requests → compare

Look for:

- Different responses
- Logic inconsistencies
- Side effects

---

### 3. Prove

- Remove noise
- Reproduce consistently
- Turn into exploit

---

## 🛡️ Prevention (Defensive Insight)

- Eliminate **sub-states**
- Use:
    - Atomic operations
    - Proper locking
    - Consistent state transitions
- Avoid:
    - Multi-step sensitive logic in one request

---

# 🧠 Mental Model (Important)

Think of race conditions as:

"Breaking the application state machine by forcing overlap"

NOT:

"Just sending many requests fast"

---

## 🚀 Quick Cheat Sheet

|Type|Key Idea|Target|
|---|---|---|
|Multi-endpoint|Multiple endpoints collide|Payment, cart|
|Single-endpoint|Same endpoint, diff inputs|Reset, email|
|Partial construction|Incomplete object state|API keys|
|Time-sensitive|Weak randomness|Tokens|
|Session locking|Requests serialized|Bypass via sessions|

---