
---

``md
> [!ABSTRACT]
> Race Condition is a vulnerability where an application behaves incorrectly when **multiple requests are processed at the same time**.  
> 
> It happens when:
> - The app processes concurrent requests without proper locking  
> - There is a gap between **check → use** (TOCTOU)  
> - Shared resources (balance, coupons, roles) are updated unsafely  
> 
> Attackers exploit this by sending **multiple requests simultaneously** to create unintended behavior. :contentReference[oaicite:0]{index=0}  
> 
> Impact:
> - Double spending (e.g., reuse coupon)  
> - Bypass limits (withdraw more than balance)  
> - Privilege escalation  
> 
> 🧠 Think: “You’re racing the server logic and winning before it updates state”

---

## 🧠 Race Conditions – Cheat Sheet

---

### 🎯 1. Basic Detection (Repeat Requests)

> [!CHECK]
> Send multiple identical requests quickly  
> Look for inconsistent or duplicated results  

```bash
POST /buy
````

```bash
POST /buy
```

---

### 🧪 2. Parallel Request Attack

> [!SUCCESS]  
> Send multiple requests at the same time

```bash
POST /apply-coupon
POST /apply-coupon
POST /apply-coupon
```

---

### 🧬 3. Double Spend (Business Logic Abuse)

> [!SUCCESS]  
> Exploit balance update race

```bash
POST /withdraw amount=100
POST /withdraw amount=100
```

---

### 🧩 4. Limit Bypass (Rate / Coupon)

> [!SUCCESS]  
> Apply action multiple times before state updates

```bash
POST /redeem-coupon code=DISCOUNT
POST /redeem-coupon code=DISCOUNT
```

---

### 🧪 5. TOCTOU (Time-of-Check to Time-of-Use)

> [!ATTENTION]  
> Exploit gap between validation and execution

```bash
POST /check-balance
POST /withdraw amount=100
```

---

### 💥 6. Account / Privilege Race

> [!SUCCESS]  
> Trigger conflicting operations

```bash
POST /upgrade-role
POST /delete-user
```

---

### ⚙️ 7. File Upload Race

> [!ATTENTION]  
> Replace file during validation

```bash
upload file.jpg
replace with shell.php
```

---

### 🧪 8. Token Reuse / Session Race

> [!SUCCESS]  
> Reuse one-time tokens multiple times

```bash
POST /reset-password token=abc
POST /reset-password token=abc
```

---

### 💣 9. Race with Delay (Timing Attack)

> [!CHECK]  
> Introduce delay to exploit window

```bash
sleep 1
POST /withdraw amount=100
```

---

### 🔍 10. Real Workflow (Operator Method)

> [!SUCCESS]

1. Identify critical action:
    

```bash
POST /withdraw
```

2. Send single request → observe behavior
    
3. Send multiple requests simultaneously:
    

```bash
POST /withdraw
POST /withdraw
```

4. Check:
    

- Balance inconsistencies
    
- Duplicate actions
    
- Unexpected results
    

5. Automate:
    

- Burp Intruder (parallel)
    
- Turbo Intruder
    

---

### 🧠 11. Where to Test

> [!ABSTRACT]

- Payment / withdrawal endpoints
    
- Coupon / discount systems
    
- Account updates
    
- File uploads
    
- Token-based actions
    

---

## 🔥 Minimal Payload Set (EXAM USE)

> [!SUCCESS]  
> If you remember nothing else

```bash
POST /withdraw amount=100
POST /withdraw amount=100

POST /redeem-coupon code=DISCOUNT
POST /redeem-coupon code=DISCOUNT

POST /reset-password token=abc
POST /reset-password token=abc
```

---

## ⚠️ Operator Notes

> [!FAILURE]  
> If nothing happens:
> 
> - Proper locking implemented
>     
> - Transactions atomic
>     
> - Rate limiting enforced
>     

---

> [!ATTENTION]  
> Race conditions depend on:
> 
> - Timing
>     
> - Concurrency
>     
> - Shared resource
>     
> 
> You need:
> 
> - A critical action
>     
> - A timing window
>     
> 
> No timing window → no exploit

---

```

---

## 🔥 Key Insight (very important)

- This is **NOT payload-heavy** like XSS/SQLi  
- It’s about:
  - **Timing**
  - **Concurrency**
  - **Business logic**


---



Just say 🚀
::contentReference[oaicite:1]{index=1}
```