**What is an SSRF?**

SSRF stands for Server-Side Request Forgery. It's a vulnerability that allows a malicious user to cause the webserver to make an additional or edited HTTP request to the resource of the attacker's choosing.

  

**Types of SSRF**

There are two types of SSRF vulnerability; the first is a regular SSRF where data is returned to the attacker's screen. The second is a Blind SSRF vulnerability where an SSRF occurs, but no information is returned to the attacker's screen.

![[ssrf_1.png]]

---

### 🧠 The Goal:

The hacker wants to trick a website into **fetching data it shouldn’t**, like **user information** or other **private data**.

---

### 💻 What's Normally Supposed to Happen?

When you visit:

```
http://website.thm/stock?url=http://api.website.thm/api/stock/item?id=123
```

The website fetches stock info **from its own API** (like product info from its warehouse). So far, everything is normal.

---

### 👿 What the Hacker Does:

1. The hacker **changes the URL** in the request to something like:
    
    ```
    http://website.thm/stock?url=http://api.website.thm/api/user
    ```
    
2. This tricks the website into thinking it needs to **go fetch sensitive user data** (like customer info) from inside its own system.
    

---

### 🛠️ What Actually Happens:

1. 💀 Hacker sends that tricky request to the website.
    
2. 🌐 The website doesn't check or stop this — it says:  
    _“Oh, I’ll go fetch the data from `api.website.thm/api/user` then!”_
    
3. 🧠 The website fetches user info and returns it.
    
4. 🔓 The hacker gets **user data** — which **they should not have access to**.
    

---

### 🧨 The Attack Name:

This is called **SSRF (Server-Side Request Forgery)**.

> It means the hacker **tricks the server** into making requests it **shouldn’t**, on the hacker's behalf.

---

### 🔐 In Real Life:

If not protected, this kind of trick can let hackers:

- Access **internal systems**
    
- Steal **private information**
    
- Even **take control of the server**
    

---

![[ssrf_2.png]]

---

### 🧠 The Goal:

The attacker wants to **access private user information** by tricking the server — even though they **can only control part of the URL path**, not the whole thing.

---

### 🪜 What the Website Thinks is Happening:

You (normally) request:

```
http://website.thm/stock?url=item?id=123
```

And the website builds the full path like:

```
http://api.website.thm/api/stock/item?id=123
```

So it fetches product/stock data. All good.

---

### 👿 What the Hacker Does:

The hacker changes the request to:

```
http://website.thm/stock?url=../user
```

That little **`../`** means “go up one folder.”

So instead of the request being:

```
http://api.website.thm/api/stock/../user
```

It becomes:

```
http://api.website.thm/api/user
```

---

### 💥 Result:

- The website **unknowingly sends a request** to `/api/user`.
    
- This gives **private user data** to the hacker instead of stock info.
    

---

### 📦 This Trick is Called:

**Directory Traversal via SSRF** (a combo of two tricks):

1. **SSRF** – fooling the server into making requests.
    
2. **Directory Traversal** – using `../` to escape directories and access forbidden files or paths.
    

---

### 🛡️ How to Stop This:

To prevent this kind of attack, developers should:

- 🔒 Validate and sanitize user input (don’t allow `../` or full URLs).
    
- 🛑 Block internal IPs or restricted paths in the server.
    
- 🌐 Use a strict allowlist of URLs or paths the server is allowed to fetch from.
    
- 🧪 Monitor for unusual URL patterns or requests.
    

---

![[ssrf_3.png]]


---

### 🧠 What’s the Goal?

The attacker wants the website to:

- **Talk to the wrong server** (one they choose)
    
- **Stop the website from adding unwanted extra parts to the URL**
    

---

### 📦 Normally:

The site is expecting a request like:

```
/stock?server=api.website.thm/api/stock/item?id=123
```

So the site builds the request like:

```
http://api.website.thm/api/stock/item?id=123
```

All good.

---

### 👿 What the Hacker Does:

Instead, they send:

```
http://website.thm/stock?server=api.website.thm/api/user&x=&id=123
```

This means:

- `server` = **api.website.thm/api/user**
    
- The `&x=` part is a **trick** — it **stops the rest** of the path from being stuck onto the end.
    

---

### 🧠 How It Works:

The site now builds this URL:

```
http://api.website.thm/api/user?x=&id=123
```

See what happened?

- Instead of continuing the path like `/api/user/api/stock/item?id=123`
    
- It turns the rest of it into **just a query string** (`?x=&id=123`)
    

💥 This means the hacker has **full control** over the URL and can make the server request **anything they want**.

---

### 🔓 The Danger:

- The attacker gets the server to talk to **sensitive internal endpoints**.
    
- They bypass built-in protections because the server _thinks_ it's doing the right thing.
    

---

### 🧠 What Makes This Clever?

The attacker used a **query string trick** (`&x=`) to stop the website from adding more path stuff.

> Think of it like cutting off a train before it gets too long — they put a blocker to stop the website from attaching more "cars" (URL parts).

---

### 🛡️ How to Protect Against It:

✅ Validate the full URL (including subdomains and paths)

✅ Don’t allow users to choose or control internal domains

✅ Block requests to:

- Internal services (like `localhost`, `127.0.0.1`)
    
- Private networks (like `10.0.0.0/8`, `192.168.0.0/16`)
    

✅ Normalize and sanitize paths (to avoid path tricks)

✅ Only allow a **hardcoded list of safe URLs** (a whitelist)

---

Would you like me to create a visual or a checklist of **top SSRF protections for developers** or **pentesters**?