---
layout: post
title: CSP Part-1
date: 2023-12-02 01:00 +0700
modified: 2023-12-03 16:49:47 +07:00
tag:
  - csp
  - xss
---

### What is XSS?

A malicious Injection of JavaScript into a webpage.

### What can you do with an XSS?

#### The attacker can do anything on that origin that the user can do

- Read Private Messages
- Tweet Pro
- Credit Card Skimming
- Fake logout to steal credentials

#### For Financial apps, Medical, Infra - it's much more serious
- steal funds
- Leak Medical Records
- Tear Down Infra

### What is CSP?

A tool to help prevent XSS exploitation.

### How does it prevent XSS exploitation?

It tells the browser which resources are allowed.

### What does CSP look like?

```
default-src 'self';
connect-src 'self'
font-src 'self' data: 
frame-src 'self' 
img-src 'self' data: https;
object-src 'none';
script-src 'self'
style-src 'self' 'unsafe-inline';
base-uri 'self';
```

### How is CSP delivered?

It's delivered through the HTTP headers.

HTTP Request

```
GET /index.html HTTP/1.1
Host: app.com
User-Agent: Mozilla/5.0
Accept-Language: en-US
Accept-Encoding: gzip, delfate
```


HTTP Response

```
HTTP/1.1 200 OK
Content-Type: Text/html
Content-LengthL 4567
Set-Cookie: user_session=abc123
Content-Security-Policy: default-src 'none', script-src 'self';

<html> <title>app.com</title></html>
```

### NodeJS / Express

```
app.use(function(req, res, next) {}
	res.setHeader("Content-security-policy-report-only", "default-src 'self'; script-src 'self' 'report-sample'; next();
});
```

### What are the different CSP Directives?

**![](https://lh7-us.googleusercontent.com/KoN3wPnNHTS3nSR2MI7jRMMFIUwuo41ki2umzwLQapezyIlAilWok1VuQdyf3z0FMth63NEb4IgJLKctO-bPpiBgavegchJQPGnfZxBMjNbhStqf28hGix238qwHb0ie9NK7rXvn2RmQ0s7kgY3gnn4vVw=s2048)**

**![](https://lh7-us.googleusercontent.com/luehXo4iiFoRv2kqnqs5U3sqpoMkm4ro9GDPSCE8Gh-Mf-wFA_Vs5cshtWabIvy-90dHGcJCLGui6UVVPN4WBwDPjWm3I9558-5N0taRfkzNE9qbVqCwgW6niGjMe-gTkPC3GLhzoBl010W4WJLfye6qIA=s2048)**

### What are the different CSP sources?

**![](https://lh7-us.googleusercontent.com/x-aPtn0ErJ6X_wRS8rb5AqLSJZW5RYvofE7JoIy_SkUSytS8Ff-rJaeSwzLja7Ic_R2YSJX5V6bafp-vgX2THtEvkrH6150jSvB0Yvh-IQyd2c__8e1cfQd0o-X73XZX_RGiV36HmSaXN1jjtSRmVT9ZPQ=s2048)**

**![](https://lh7-us.googleusercontent.com/DtnbiOahI0Q1iQ0YexXkoNH8nePir4QN4fj48ZIn8wR3Vs-fubDLx9e-LfjV3BPBWVa_WSNwpZsJ6qxjEDQ4OAurBJQVgafxlz32esV44WOjm0FZDgdfJg3aPYAjR9PFFKbXqyZplS9RReWqWj3gaCotyQ=s2048)**

### What is in a Content Security Policy?

A list of 'directives' which contain 'sources'

### How does specifying script-src prevent an inline XSS?

Inline scripts (and eval) are blocked

### What is blocked with a standard Content-Security-Policy?

![def-package](/_posts/CSP-101/def-package.png)

Blocked:

```
<script src="https://evil.com/malicious.js"></script>

<button onclick="xxx"></button> //CSP does not allow this

<script> //CSP does not allow this because it is an inline JS
	// app code
</script>
```

- Because there's no way to differentiate between malicious Inline JavaScript and allowed Inline JavaScript.

### What to do about inline scripts?

<p style="align:center";> Move to a file </p>

![csp01](/_posts/CSP-101/csp02.png)


### What if I can't move all the JavaScript?

| sha256-abc123   | Hash the JavaScript and include the hash-source                              |     |
| --------------- | ---------------------------------------------------------------------------- | --- |
|                 | <script>normalAppCode</script>                                               |     |
|                 | Does not work on event_handlers, to include those, include 'unsafe-hashes'   |     |
| nonce-abc-123   | Generate a random unique token on each request and add it to the script tags |     |
|                 | <script nonce='abc123'>normalAppCode</script>                                |     |
| 'unsafe-inline' | Disable CSP's XSS protection (not recommended)                               |     |

### What about eval?

| 'unsafe-eval' | Allows the use of eval |
| ------------- | ---------------------- |
|               | eval(), Function(''), setTimeout('')|




