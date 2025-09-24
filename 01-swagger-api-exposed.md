
# SECURE BANK WAPT  
**Report – Swagger API **  
---

## Issue #1 – Swagger API Exposed

###  Summary
The application has publicly exposed Swagger API documentation, which can be discovered through directory enumeration. This exposes potentially sensitive information about the backend API structure, endpoints, and request/response formats, increasing the attack surface.

---

###  Discovery Method

Using an enumeration tool such as `gobuster`:

```bash
git clone --depth 1 https://github.com/danielmiessler/SecLists.git

gobuster dir -w SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http://localhost:1337

gobuster dir -w SecLists/Discovery/Web-Content/api/api-endpoints.txt -u http://localhost:1337
```

The following resources were discovered:

```
http://localhost:1337/swagger/index.html
http://localhost:1337/swagger/v1/swagger.json
```

These contain full Swagger/OpenAPI documentation for the application.

---

### ️ Risk

The Swagger UI and JSON schema expose:

- Full list of available API endpoints
- HTTP methods and paths
- Data models and schemas
- Input validation rules
- Potential hidden or debug endpoints

This information can be used by attackers to:

- Map the API surface for further attacks (e.g., fuzzing, injection)
- Bypass client-side restrictions
- Craft requests without the official client

---


###  HTTP Request / Response Evidence

#### `GET /swagger/index.html`

**Request:**
```
GET /swagger/index.html HTTP/1.1
Host: localhost:1337
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/132.0.0.0 Safari/537.36
```

**Response:**
```
HTTP/1.1 200 OK
Content-Type: text/html;charset=utf-8
Date: Fri, 04 Jul 2025 17:28:37 GMT
Server: Kestrel
Content-Length: 4755

<!-- HTML for static distribution bundle build -->
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Swagger UI</title>
...
```

---

#### `GET /swagger/v1/swagger.json`

**Request:**
```
GET /swagger/v1/swagger.json HTTP/1.1
Host: localhost:1337
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/132.0.0.0 Safari/537.36
```

**Response:**
```
HTTP/1.1 200 OK
Content-Type: application/json;charset=utf-8
Date: Sat, 05 Jul 2025 15:18:22 GMT
Server: Kestrel
Content-Length: 27069

{
  "openapi": "3.0.1",
  "info": {
    "title": "BankWeb API",
    "version": "v1"
  },
  ...
```

---

###  Recommendations

- **Disable Swagger UI and JSON documentation** in production environments.
- If needed for developers, restrict access via:
  - IP allowlisting
  - Authentication and authorization
- Regularly audit public resources and perform content discovery scans.
- Set up security headers (e.g., `X-Content-Type-Options`, `X-Frame-Options`) to reduce risk.

---

##  CVSS & Risk Classification

```text
* Information Disclosure
** Issue #1 - Swagger API Exposed

:PROPERTIES:
:CVSS: CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:L/I:N/A:N
:RISK: Medium
:END:
```

---

**Status:**  Vulnerability Confirmed  
**Severity:** Medium – Based on CVSS 3.1 Vector
