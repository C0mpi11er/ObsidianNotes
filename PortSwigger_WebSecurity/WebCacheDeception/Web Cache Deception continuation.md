
Delimeter decrepances
==

use the port swigger delimeter list 
https://portswigger.net/web-security/web-cache-deception/wcd-lab-delimiter-list

then check which one doesnt return and error after using intruder then construct payload from there 

e.g
<script>document.location"https://url;foobar.css"</script>


Exploiting static directory cache rules
==

---

# Web Cache Deception – Parsing Discrepancies

## 1. Delimiter Decoding Discrepancies

Websites sometimes **URL-encode special characters** because they have special meaning in URLs.

Example encodings:

| Character | Encoded |
| --------- | ------- |
| `#`       | `%23`   |
| `?`       | `%3F`   |
| `/`       | `%2F`   |

Problem:  
**Cache and origin server may decode URLs differently.**

This causes them to **interpret the same URL differently.**

---

### Example

Request:

```
/profile%23wcd.css
```

Cache behavior:

```
Interprets: /profile%23wcd.css
Sees .css → static file
Stores response
```

Origin server behavior:

```
Decodes %23 → #
Interprets path: /profile
Returns profile data
```

Result:

```
Cache stores sensitive data under:
/profile%23wcd.css
```

This is **Web Cache Deception**.

---

### Another Example

Request:

```
/myaccount%3fwcd.css
```

Cache:

```
Sees: /myaccount%3fwcd.css
.css extension → cache response
```

Cache forwards decoded request:

```
/myaccount?wcd.css
```

Origin server:

```
? starts query
Interprets path as:
/myaccount
```

Result:

```
Dynamic response cached as static file
```

---

### Useful Encoded Delimiters to Test

```
%23   # fragment
%3F   ? query delimiter
%2F   / path separator
%3B   ; parameter delimiter
%26   & query separator
```

Also test **non-printable characters**:

```
%00   null byte
%0A   newline
%09   tab
```

These may **truncate URL paths** after decoding.

---

# Static Directory Cache Rules

Caches often store resources in directories like:

```
/static/
/assets/
/images/
/scripts/
```

Example rule:

```
/assets/* → cache
```

If a dynamic endpoint can be interpreted as belonging to these directories, **sensitive data may be cached**.

---

# Normalization Discrepancies

Normalization converts URLs into a standard format.

Example:

```
/static/../profile
```

Normalizes to:

```
/profile
```

Different systems normalize differently.

---

### Example

Request:

```
/static/..%2fprofile
```

Cache interpretation:

```
/static/..%2fprofile
Prefix /static → cached
```

Origin server interpretation:

```
decode %2f → /
/static/../profile
normalize → /profile
```

Result:

```
Dynamic profile page cached as static resource
```

---

# Important Note

Browsers automatically resolve traversal like:

```
../
```

So attackers encode it:

```
..%2f
```

to prevent browser normalization.

---

# Detection Strategy

1. Identify cached resources (CSS, JS, images).
    
2. Test encoded delimiters.
    
3. Test path traversal payloads.
    

Examples:

```
/profile%23test.css
/profile%3ftest.css
/assets/..%2fprofile
/static/..%2faccount
```

Look for:

```
dynamic data returned
+ response cached
```

---

✅ **Core Principle**

```
Cache thinks request is static
Server treats request as dynamic
→ Sensitive data becomes cached
```

---
Cache Normalization Discrepancy
==
Exploit occurs when:

Cache → resolves encoded path traversal (normalizes)  
Origin Server → does NOT resolve traversal

This creates **different interpretations of the same URL**.

---

## Payload Structure

/<dynamic-path>%2f%2e%2e%2f<static-directory>

Encoded traversal:

%2f = /  
%2e = .  
%2f%2e%2e%2f = /../

Example:

/profile%2f%2e%2e%2fstatic

---

## Problem

Cache interpretation:

/profile/../static → /static

Origin server interpretation:

/profile%2f%2e%2e%2fstatic

Result:

Server returns error → exploit fails

Traversal alone is **not enough**.

---

# Using a Delimiter

Add a delimiter recognized by the **origin server but not the cache**.

Example delimiter:

;

Payload:

/profile;%2f%2e%2e%2fstatic

---

## Interpretation

Origin server:

/profile;%2f%2e%2e%2fstatic  
↓  
; acts as delimiter  
↓  
/profile

Returns:

Dynamic profile data

Cache:

/profile;%2f%2e%2e%2fstatic  
↓ decode traversal  
/profile;/../static  
↓ normalize  
/static

Cache stores response.

---

# Result

Cache thinks → /static  
Server processes → /profile

Dynamic response becomes **cached as static content**.

---

# Important

Always encode traversal:

%2f%2e%2e%2f

Reasons:

- Prevent browser normalization
    
- Avoid delimiter conflicts
    
- Ensure cache performs the decoding
    

---

# Exploit Pattern

 
 <dynamic-path>;<encoded traversal><static-directory>

Example:

/profile;%2f%2e%2e%2fstatic


---

✅ **Core Idea**

Cache → resolves traversal  
Server → truncates with delimiter  
→ Dynamic data cached as static resource




# how to prevent web cache deception
==
#disable cacheing

 The definitive way to prevent web cache poisoning would clearly be to disable caching altogether. While for many websites this might not be a realistic option, in other cases, it might be feasible. For example, if you only use caching because it was switched on by default when you adopted a CDN, it might be worth evaluating whether the default caching options really do reflect your needs.

Even if you do need to use caching, restricting it to purely static responses is also effective, provided you are sufficiently wary about what you class as "static". For instance, make sure that an attacker can't trick the back-end server into retrieving their malicious version of a static resource instead of the genuine one.

This is also related to a wider point about web security. Most websites now incorporate a variety of third-party technologies into both their development processes and day-to-day operations. No matter how robust your own internal security posture may be, as soon as you incorporate third-party technology into your environment, you are relying on its developers also being as security-conscious as you are. On the basis that you are only as secure as your weakest point, it is vital to make sure that you fully understand the security implications of any third-party technology before you integrate it.

Specifically in the context of web cache poisoning, this not only means deciding whether to leave caching switched on by default, but also looking at which headers are supported by your CDN, for example. Several of the web cache poisoning vulnerabilities discussed above are exposed because an attacker is able to manipulate a series of obscure request headers, many of which are entirely unnecessary for the website's functionality. Again, you may be exposing yourself to these kinds of attacks without realizing, purely because you have implemented some technology that supports these unkeyed inputs by default. If a header isn't needed for the site to work, then it should be disabled.

You should also take the following precautions when implementing caching:

    If you are considering excluding something from the cache key for performance reasons, rewrite the request instead.
    
    Don't accept fat GET requests. Be aware that some third-party technologies may permit this by default.
    Patch client-side vulnerabilities even if they seem unexploitable. 
    Some of these vulnerabilities might actually be exploitable due to unpredictable quirks in your cache's behavior. It could be a matter of time before someone finds a quirk, whether it be cache-based or otherwise, that makes this vulnerability exploitable.
