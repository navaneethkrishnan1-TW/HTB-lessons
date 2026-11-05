# ⚡ Quick Reference: Web Requests & cURL Cheat Sheet

A cheat sheet for HTTP inspection, DevTools tricks, and cURL commands used during web application penetration testing.

---

## 🛠️ cURL Flags Quick Reference

| Flag | Name | Purpose | Example |
| :---: | :--- | :--- | :--- |
| `-v` | Verbose | Show full request & response headers | `curl -v http://target/` |
| `-i` | Include | Include HTTP response headers in output | `curl -i http://target/` |
| `-I` | Head | Send a `HEAD` request (headers only) | `curl -I http://target/` |
| `-s` | Silent | Hide progress meter and errors | `curl -s http://target/ \| jq` |
| `-u` | User | Basic HTTP Authentication (`user:pass`) | `curl -u admin:admin http://target/` |
| `-H` | Header | Add custom request headers | `curl -H "Content-Type: application/json" ...` |
| `-A` | User-Agent | Spoof User-Agent string | `curl -A "Mozilla/5.0" http://target/` |
| `-X` | Method | Specify HTTP request method | `curl -X PUT http://target/api/user` |
| `-d` | Data | Send HTTP POST/PUT payload | `curl -d "user=admin&pass=admin" ...` |
| `-c` | Cookie Jar | Write received cookies to a file | `curl -c cookies.txt -d "..." http://target/login` |
| `-b` | Cookie | Send cookies from file or string | `curl -b cookies.txt http://target/dashboard` |

---

## 📋 Common Exploitation & Testing Snippets

### 1. Basic Auth Bypass / Test
```bash
# Pass user and password directly
curl -s -u admin:admin http://target:port/

# Or pass manually via Authorization header
curl -s -H "Authorization: Basic YWRtaW46YWRtaW4=" http://target:port/
```

### 2. Session Cookie Capture & Reuse
```bash
# Step 1: Login and save session cookie
curl -s -c /tmp/cookies.txt -X POST -d "username=admin&password=admin" http://target:port/

# Step 2: Use saved session cookie on protected endpoint
curl -s -b /tmp/cookies.txt http://target:port/dashboard.php
```

### 3. JSON API Interaction
```bash
# POST JSON query
curl -s -b /tmp/cookies.txt -X POST \
  -H "Content-Type: application/json" \
  -d '{"search":"flag"}' \
  http://target:port/search.php
```

### 4. RESTful CRUD API Manipulation
```bash
# CREATE (POST)
curl -s -X POST http://target:port/api.php/city/ \
  -H "Content-Type: application/json" \
  -d '{"city_name":"MyCity", "country_name":"HTB"}'

# READ (GET)
curl -s http://target:port/api.php/city/MyCity | jq

# UPDATE (PUT)
curl -s -X PUT http://target:port/api.php/city/MyCity \
  -H "Content-Type: application/json" \
  -d '{"city_name":"UpdatedCity", "country_name":"HTB"}'

# DELETE (DELETE)
curl -s -X DELETE http://target:port/api.php/city/UpdatedCity
```

---

## 🛡️ Critical Security Headers to Check

| Header | Secure Value | Threat Mitigated |
| :--- | :--- | :--- |
| `Content-Security-Policy` | `default-src 'self'; ...` | Cross-Site Scripting (XSS), Data Injection |
| `Strict-Transport-Security` | `max-age=31536000; includeSubDomains` | Man-in-the-Middle (MitM), SSL Strip |
| `X-Content-Type-Options` | `nosniff` | MIME-type confusion / File Upload XSS |
| `X-Frame-Options` | `DENY` or `SAMEORIGIN` | Clickjacking attacks |
| `Access-Control-Allow-Origin` | Explicit trusted domain (not `*`) | Insecure Cross-Origin Resource Sharing (CORS) |
