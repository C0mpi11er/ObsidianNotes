#note 
Example rules used by caches:

Cache files ending in:  
.css  
.js  
.jpg  
.png
.exe
.ico

 Web cache deception is a vulnerability that enables an attacker to trick a web cache into storing sensitive, dynamic content. It's caused by discrepancies between how the cache server and origin server handle requests.

In a web cache deception attack, an attacker persuades a victim to visit a malicious URL, inducing the victim's browser to make an ambiguous request for sensitive content. The cache misinterprets this as a request for a static resource and stores the response. The attacker can then request the same URL to access the cached response, gaining unauthorized access to private information. 


Web caches
=
A web cache is a system that sits between the origin server and the user. When a client requests a static resource, the request is first directed to the cache. If the cache doesn't contain a copy of the resource (known as a cache miss), the request is forwarded to the origin server, which processes and responds to the request. The response is then sent to the cache before being sent to the user. The cache uses a preconfigured set of rules to determine whether to store the response.

When a request for the same static resource is made in the future, the cache serves the stored copy of the response directly to the user (known as a cache hit). 



Cache keys
=
When the cache receives an HTTP request, it must decide whether there is a cached response that it can serve directly, or whether it has to forward the request to the origin server. The cache makes this decision by generating a 'cache key' from elements of the HTTP request. Typically, this includes the URL path and query parameters, but it can also include a variety of other elements like headers and content type.

If the incoming request's cache key matches that of a previous request, the cache considers them to be equivalent and serves a copy of the cached response. 




Cache rules
=
Cache rules determine what can be cached and for how long. Cache rules are often set up to store static resources, which generally don't change frequently and are reused across multiple pages. Dynamic content is not cached as it's more likely to contain sensitive information, ensuring users get the latest data directly from the server.

Web cache deception attacks exploit how cache rules are applied, so it's important to know about some different types of rules, particularly those based on defined strings in the URL path of the request. For example:

    Static file extension rules - These rules match the file extension of the requested resource, for example .css for stylesheets or .js for JavaScript files.
    Static directory rules - These rules match all URL paths that start with a specific prefix. These are often used to target specific directories that contain only static resources, for example /static or /assets.
    File name rules - These rules match specific file names to target files that are universally required for web operations and change rarely, such as robots.txt and favicon.ico.




## File Name Rules

Cache specific common files.

Examples:

robots.txt  
favicon.ico




# Basic Web Cache Deception Attack Methodology

## Step 1 — Find Dynamic Endpoint

Look for endpoints returning **sensitive data**.

Examples:

/account  
/profile  
/api/orders  
/dashboard

Focus on methods that can be cached:

GET  
HEAD  
OPTIONS

Use **Burp Proxy** to inspect responses.

---

## Step 2 — Find Parsing Discrepancy

Look for differences in how cache and server:

map URL paths  
handle delimiters  
normalize paths

---

## Step 3 — Craft Malicious URL

Example transformation:

Original:  
/api/orders/123  
  
Malicious:  
/api/orders/123/foo.css

If:

Origin server → ignores foo.css  
Cache → sees .css → caches response

Then the **sensitive response becomes cached**.

---

# Cache Buster (Important During Testing)

Caches may return **old responses** during testing.

Use a **cache buster** so each request is unique.

Example:

/api/orders/123/foo.css?cb=123  
/api/orders/123/foo.css?cb=456

Burp tool:

**Param Miner → Add Dynamic Cachebuster**

This automatically appends random query parameters.

---

# Detecting Cached Responses

Look for caching indicators in **response headers**.

### X-Cache Header

Common values:

X-Cache: hit      → served from cache  
X-Cache: miss     → fetched from origin  
X-Cache: dynamic  → not cacheable  
X-Cache: refresh  → cache revalidated

---

### Cache-Control Header

Example:

Cache-Control: public, max-age=3600

Meaning:

Response may be cached for 1 hour

---

### Response Time Difference

Cached responses are usually **much faster**.

Example:

Origin response → 800ms  
Cached response → 50ms

---

# Path Mapping Discrepancies

Two common URL mapping styles:

## Traditional Mapping

Direct file mapping.

Example:

/files/resource.html

Represents an actual file.

---

## REST Style Mapping

Logical endpoints instead of file paths.

Example:

/api/orders/123

Server processes the endpoint dynamically.

---

# Example Web Cache Deception

URL sent to victim:

/user/123/profile/wcd.css

### Origin Server (REST)

Interprets as:

/user/123/profile

Returns user profile data.

---

### Cache Server

Interprets as:

/user/123/profile/wcd.css

Because it ends with `.css`:

Cache stores response

---

### Attacker Request

Attacker visits same URL:

/user/123/profile/wcd.css

Cache returns:

Victim profile data

---

# Testing Method (Quick Field Workflow)

1️⃣ Identify sensitive endpoint

/api/orders/123

---

2️⃣ Add arbitrary segment

/api/orders/123/foo

If same response → server ignores segment.

---

3️⃣ Add static extension

/api/orders/123/foo.js  
/api/orders/123/foo.css  
/api/orders/123/foo.ico

---

4️⃣ Check response headers

Look for:

X-Cache: hit

---

5️⃣ Retrieve cached response

Use **Burp Repeater** to confirm.

---

# Useful Extensions

Tool inside:

Burp Suite

Helpful extensions:

- **Param Miner**
    
- **Web Cache Deception Scanner**
    

---

# Quick Memory Trick

**Web Cache Deception =**

Make a dynamic page look like a static file  
so the cache stores sensitive data.