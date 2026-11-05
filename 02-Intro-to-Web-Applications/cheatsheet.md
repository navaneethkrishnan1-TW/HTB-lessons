# 🛠️ Introduction to Web Applications — Cheatsheet & Quick Reference

A concise reference guide covering web application anatomy, frontend/backend technologies, common vulnerabilities, CVSS scoring, and essential reconnaissance commands.

---

## 1. Web Architecture & Solution Stacks

### Tiered Architecture
* **Presentation Tier (Front End):** Client browser executing HTML5, CSS3, and JavaScript.
* **Logic Tier (Back End):** Web server (Nginx, Apache, IIS) and application runtime (PHP, Node.js, Python, Java, C#).
* **Data Tier (Database):** Storage layer handling data persistence (SQL or NoSQL).

### Common Technology Stacks
| Stack | OS | Web Server | Database | Backend Language |
| :--- | :--- | :--- | :--- | :--- |
| **LAMP** | Linux | Apache | MySQL | PHP |
| **WAMP** | Windows | Apache | MySQL | PHP |
| **WINS** | Windows | IIS | SQL Server (MSSQL) | .NET (C#) |
| **MAMP** | macOS | Apache | MySQL | PHP |
| **MEAN** | Any | Node.js (Express) | MongoDB (NoSQL) | JavaScript / TypeScript |
| **XAMPP** | Cross-Platform | Apache | MariaDB / MySQL | PHP / Perl |

---

## 2. HTTP Status Codes

| Code | Name | Meaning | Security / Pentest Relevance |
| :---: | :--- | :--- | :--- |
| **200** | `OK` | Request succeeded | Standard successful response. |
| **201** | `Created` | New resource created | Returned after successful `POST` or `PUT` resource generation. |
| **301** | `Moved Permanently` | Resource moved to new URL | Check redirect targets (`Location` header). |
| **302** | `Found` | Temporary redirect | Common after login authentication flows. |
| **400** | `Bad Request` | Malformed request | Useful for detecting strict API schemas. |
| **401** | `Unauthorized` | Authentication required | Missing or invalid auth token/credentials. |
| **403** | `Forbidden` | Permission denied | Valid auth, but insufficient privileges (authorization failure). |
| **404** | `Not Found` | Resource does not exist | Directory fuzzing baseline status. |
| **500** | `Internal Server Error` | Server crashed processing request | High-value signal: unhandled exception, SQLi, or command injection. |

---

## 3. Databases: SQL vs NoSQL

### Relational Databases (SQL)
* **Structure:** Tabular (Tables, Rows, Columns, Primary/Foreign Keys).
* **Query Language:** SQL (Structured Query Language).
* **Examples:** MySQL, PostgreSQL, MSSQL, Oracle Database, SQLite, MariaDB.
* **Common Vulnerability:** SQL Injection (SQLi) via raw string concatenation.

### Non-Relational Databases (NoSQL)
* **Structure:** Dynamic schemas; no tables or relations.
* **Models:**
  * **Document-Based:** JSON/BSON documents (e.g., MongoDB, CouchDB).
  * **Key-Value:** String keys mapping to values/objects (e.g., Redis, DynamoDB).
  * **Wide-Column:** Distributed sparse tables (e.g., Apache Cassandra).
  * **Graph:** Nodes and edges (e.g., Neo4j).
  * **Cloud Realtime:** JSON tree structures (e.g., Google Firebase Database).
* **Common Vulnerability:** NoSQL Operator Injection (e.g. `{"$gt": ""}`).

---

## 4. Front-End Vulnerabilities & Injections

### HTML Injection (HTMLi)
* **Mechanism:** Unsanitized user input rendered directly into the HTML DOM.
* **Impact:** Website defacement, injected phishing login forms, clickjacking.
* **Test Payload:**
  ```html
  <a href="http://evil.com">Click Me</a>
  <h1>Defaced</h1>
  ```

### Cross-Site Scripting (XSS)
| Type | Execution Context | Example Vector |
| :--- | :--- | :--- |
| **Reflected XSS** | Payload in immediate request; echoed immediately by server. | `https://target.com/search?q=<script>alert(1)</script>` |
| **Stored XSS** | Payload stored in backend database; executes on viewing. | Comments, forum posts, user profile bios. |
| **DOM XSS** | Entirely client-side; source to sink execution in DOM. | `#"><img src=x onerror=alert(document.cookie)>` |

### Cross-Site Request Forgery (CSRF)
* **Mechanism:** Forcing an authenticated browser to send unauthorized HTTP requests with victim's session cookies.
* **Impact:** Unauthorized password resets, email modification, funds transfer.
* **Remediation:**
  * Unique, unpredictable **Anti-CSRF Tokens** validated per state-changing request.
  * **`SameSite` Cookie Attribute:** `SameSite=Strict` or `SameSite=Lax`.
  * Requiring password confirmation for critical actions.

---

## 5. Input Defense: Sanitization vs Validation

| Defense | Definition | Example |
| :--- | :--- | :--- |
| **Validation** | Ensuring input strictly conforms to expected data type/format before processing. | Checking that an email field matches a strict regex `^[a-zA-Z0-9_.+-]+@[a-zA-Z0-9-]+\.[a-zA-Z0-9-.]+$`. |
| **Sanitization** | Stripping or escaping unsafe/special characters from input or output. | HTML-entity encoding `<` to `&lt;` and `>` to `&gt;` before rendering. |

---

## 6. Public CVEs & CVSS Scoring

### CVSS Severity Ratings
| Severity | Score Range |
| :--- | :---: |
| **None** | `0.0` |
| **Low** | `0.1 – 3.9` |
| **Medium** | `4.0 – 6.9` |
| **High** | `7.0 – 8.9` |
| **Critical** | `9.0 – 10.0` |

### Key Vulnerability Case Studies
* **CVE-2014-6271 (Shellshock):**
  * **Category:** Command Injection in GNU Bash environment variable parsing.
  * **Impact:** Remote Code Execution via Apache CGI scripts.
* **CVE-2017-0144 (EternalBlue):**
  * **Category:** Remote Code Execution in Microsoft SMBv1 server.
  * **CVSS v2 Base Score:** `9.3` (`AV:N/AC:M/Au:N/C:C/I:C/A:C`).

---

## 7. Essential Terminal Recon Commands

```bash
# 1. Fetch raw page source without a browser (inspect for hidden comments)
curl -s http://TARGET:PORT/

# 2. Grep for developer notes, TODOs, and credentials
curl -s http://TARGET:PORT/ | grep -iE "todo|admin|pass|key|api|flag"

# 3. Inspect HTTP response headers (server type, cookies, status codes)
curl -s -i http://TARGET:PORT/

# 4. Query REST API parameter endpoints
curl -s "http://TARGET:PORT/index.php?id=1"

# 5. Test basic XSS payload reflection
curl -s "http://TARGET:PORT/index.php?search=%3Cscript%3Ealert(1)%3C/script%3E" | grep "alert"
```
