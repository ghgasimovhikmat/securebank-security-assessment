
# SECURE BANK  
**Report – Directory Listing**  


---

## Issue #2 – Directory Listing

### Summary
An exposed `/docs/` folder was discovered through directory enumeration using `gobuster`. This folder contains sensitive files accessible without any authentication or authorization. Additionally, this folder is indirectly referenced via a link in the registration page pointing to a document (`legal.pdf`), which further reveals its location.

---

###  Discovery Method

```bash
git clone --depth 1 https://github.com/danielmiessler/SecLists.git

gobuster dir -w SecLists/Discovery/Web-Content/directory-list-2.3-medium.txt -u http://localhost:1337

gobuster dir -w SecLists/Discovery/Web-Content/api/api-endpoints.txt -u http://localhost:1337
```

During enumeration, the `/docs/` folder was discovered:

- Accessing `http://localhost:1337/docs/` shows a **directory listing**.
- Also referenced indirectly via the registration page's *Terms and Conditions* link:
  ```
  http://localhost:1337/docs/legal.pdf
  ```



---

###  HTTP Request / Response Evidence

#### `GET /docs/`

**Request:**
```
GET /docs/ HTTP/1.1
Host: localhost:1337
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/132.0.0.0 Safari/537.36
```

**Response:**
```
HTTP/1.1 200 OK
Content-Length: 2335
Content-Type: text/html; charset=utf-8
Date: Sat, 05 Jul 2025 15:20:08 GMT
Server: Kestrel

<!DOCTYPE html>
<html lang="iv">
<head>
  <title>Index of /docs/</title>
...
<tbody>
     <tr class="file">
         <td class="name"><a href="./legal.pdf">legal.pdf</a></td>
         <td class="length">145,119</td>
         <td class="modified">06/26/2025 07:13:48 +00:00</td>
     </tr>
     <tr class="file">
         <td class="name"><a href="./privacy policy.pdf">privacy policy.pdf</a></td>
         <td class="length">18,005</td>
         <td class="modified">06/26/2025 07:13:48 +00:00</td>
     </tr>
     <tr class="file">
         <td class="name"><a href="./meeting-2018.11.21.pdf">meeting-2018.11.21.pdf</a></td>
         <td class="length">3,287</td>
         <td class="modified">06/26/2025 07:13:48 +00:00</td>
     </tr>
</tbody>
...
```

---

#### `GET /docs/meeting-2018.11.21.pdf`

**Request:**
```
GET /docs/meeting-2018.11.21.pdf HTTP/1.1
Host: localhost:1337
User-Agent: Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/132.0.0.0 Safari/537.36
```

**Response:**
```
HTTP/1.1 200 OK
Content-Length: 3287
Content-Type: application/pdf
Date: Sat, 05 Jul 2025 15:21:23 GMT
Server: Kestrel
Accept-Ranges: bytes
ETag: "1dbe669dc53d2d7"
Last-Modified: Thu, 26 Jun 2025 07:13:48 GMT

%PDF-1.4
%Çì¢
...
```

---

###  Recommendations

- Disable directory listing on the web server (adjust server configuration).
- Apply access controls to sensitive folders and documents.
- Move internal documents outside the web root if not needed publicly.
- Regularly audit file permissions and access points to static content.

---

##  CVSS & Risk Classification

```text
* Information Disclosure
** Issue #2 - Directory Listing

:PROPERTIES:
:CVSS: CVSS:3.1/AV:N/AC:L/PR:N/UI:N/S:U/C:L/I:N/A:N
:RISK: Medium
:END:
```

---

**Status:** Vulnerability Confirmed  
**Severity:** Medium – Based on CVSS 3.1 Vector
