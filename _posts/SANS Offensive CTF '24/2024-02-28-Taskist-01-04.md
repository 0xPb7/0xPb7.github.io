---
title: Taskist 01 - 04
date: 2024-02-28 09:45:47 +07:00
modified: 2024-02-28 09:24:47 +07:00
tags: [blog, SANS-CTF, 2024, IDOR, SSRF]
---

**SANS Offensive CTF - Write Up**

**Date:** 29th Feb 2024 **Author:** 0xPb, daniel1895
**Application:** taskist

---

## Summary:
This report outlines several vulnerabilities discovered within the application, including IDOR (Insecure Direct Object Reference), privilege escalation, SSRF (Server-Side Request Forgery), and unauthorized file access issues. These vulnerabilities pose a significant risk to the confidentiality, integrity, and availability of the application and its data.

  
## Vulnerability Details:

### Taskist:01: IDOR Vulnerability in “/api/tasks/<ID>”

  

**Description:** The application has an Insecure Direct Object Reference vulnerability in the /api/tasks/64 endpoint, allowing unauthorized access to sensitive information.

  

**Impact:** Admin notes containing confidential information, including the flag, are exposed to unauthorized users.

  

**Recommendation:** Implement proper access controls and authorization mechanisms to restrict access to sensitive data based on user roles and permissions.

  

**POC:**

1. Now after logging into the application you can see the source code (ctrl + u), observe the endpoints/paths.

  

![](https://lh7-us.googleusercontent.com/eMLbCdUziml1Pf0tJD63bJ_gN35t4zHshTZXnnlQ3QvBS3aM1nMCceJn7OfvgQKBU1vyDSQetX8sh3P8Vh1A1b0tRSOUZYbf9BT7K-USgCIQgcgoz3-rbwDGOlZRnd4BSEkslKz-XYJ_k4TwToz_t74)

  

2. After observing you can find the only endpoint which is potential to IDOR. Sending the request to Intruder along with the first 100 numbers as payloads and start the attack.

  

![](https://lh7-us.googleusercontent.com/nxR9y5peTKcPulloDP9k01p3UCJDhXqBJ90lgNxIGiCeBZKihpO950_TA-RquNFe5mUs8EMzPnsfqDnLfDeKLG_ITHyW_cH1b7BHRqMZ08OFxHsev_pa7RPHyJhSyRu_7rY4iaP8vmS7JIwLYoayZc8)

  
  
  

### Taskist:02: Privilege Escalation via Update Password Feature

  

**Description:** The application allows changing the user_id parameter to an admin user's user_id during the update password feature, leading to unauthorized privilege escalation.

  

**Impact:** Unauthorized users can change the admin's password, compromising the admin account's security.

  

**Recommendation:** Implement strict validation checks to ensure that only authorized users can update passwords, and enforce proper authentication mechanisms to prevent privilege escalation attacks.

  

**POC:**

1. Navigate to Password Reset Functionality, Due to restrictions we are unable to change the username on Frontend. But Intercepted the request and observed that “user_id” is being passed. Change the “user_id” value to admin’s user_id, which we have observed from previous requests.

  

![](https://lh7-us.googleusercontent.com/-NzmWJF56QChRiOrVMOP-paIM0t41k45PnMd1ozod0kWz0rpqKXnSUUQv1YlqIB0cN5bGVk-Dg-xuYew62X45_DKTCmtWQQCM5NWgf9zfe9X34qyCrpqvLi9oMr4v8zqSfglfGcMGK9A2Z29oBgyWPg)

  

![](https://lh7-us.googleusercontent.com/GOAlSzuo6EpOpYswM6jF38Cs9lp1HiWW_J116RHrNEwklRyWIsCZ_pgCIteYOBikGVryZr9tIXPlVKmtxGDAwrsCERG2OjLarPLpWcem5LwHtb0bsWbiS0Fyqcd2YgmU0mYXOBjQMzipPIChPyFJoV8)

  

![](https://lh7-us.googleusercontent.com/JCH36bLc4GhtfE8JqQU2hiTAuBkgu1CCwzi0KscS3pD8iv-gBsT7ppR_gIRZUhVve3OfMkFsbCO4IZWggP9E2wwJJ-yAS8esIygaN8ngNAzKLsiAmSPIb_aBASf2tjm9nxzEAGWdsGAFPul1guMC6PM)

  

### Taskist:03: SSRF Vulnerability leads to Unauthorized File Read Access

  

**Description:** By exploiting a vulnerability in the application's handling of the proc/self/environ path, an attacker can gain unauthorized access to sensitive files, such as /app/index.js.

  

**Impact:** Attackers can read sensitive application files containing configuration details or other critical information.

  

**Recommendation:** Implement proper input validation and sanitization to prevent directory traversal attacks, and restrict access to sensitive files to authorized users only.

  

**POC:**

1. After logging in as an admin, we can observe that there are `site configuration` settings along with import and export features.

  
  
  

2. Now by analyzing the source code of `site_config.js`, we can observe that the import feature is validating to variables not to be as ‘undefined’.

  

![](https://lh7-us.googleusercontent.com/ZkU0EY1sWcaKI3_uTGPj7Qqf6PNAqc_vDg0y76e8EIhFXum8DvzF0glpaQxeXmSBmY1WSEd-P6VeSl3F5N1EF3AIl4RZGvd53JfkhABwj6rwhScLrxJYivNf5YGOPsdy8I40L3X4ah53aMImaI5Nr6o)

  
  
  

3. Now after exploring the export functionality we are able to download `site-config.json`. When we look at the variables of the json file, the validating variables of import feature and export json file are the same for the first 2 variables. Here we can conclude that the vulnerability should be exploited over here.

  

4. By Uploading the dummy file and intercepting the request, replace the url with burp collaborator. We got a hit!, SSRF Confirmed.

![](https://lh7-us.googleusercontent.com/6EUEjDBEwtaHRoikozKgWLJc3YGp-s8lVXx6XCPpAo1yiduxqn776XIiggi2AtUmXKvwEQfPUu5n2WPooXNGX8LOhss2DMos67Ak6pNa1E69VThYTuZg6jRCF5HxuLnhUa935S9sNueNK3mz3z5afLo)

  
  

5. We should look for sensitive information files which disclose the endpoints of the server function. By analyzing the tasks list in the admin dashboard we can say that 2 out 

3 are pending. A Hint to the “/app” directory, which is not accomplished by admin.

  
  

![](https://lh7-us.googleusercontent.com/TB__p-q19IsVcAbiMuzTbPB2zDJoRFHhy_f5RlG7K6K5sB2jw062LRfSw7wDZjGcWj0tZmTlBV07D7DC0ss7btIVuoNdrjfNFwSg3AosLSwE9xuUAAbV9OQEoHb9XmnQ98NyoAZ8U5WSnWsiHOv1GCw)

  
  
  

6. Brute forcing with common LFI Payloads!??no, we should look for sensitive endpoints, some of sensitive endpoints are “/proc/*/*”.

  
  

![](https://lh7-us.googleusercontent.com/fLJp7rCcyWe-GvrXhzX52Ud7TTgYGl1wRH-ewF_jUyj_5x7KSUAmE5fkECsI5Lw-84XlA36trU0o36DW-OlxRHqo8PndAxYvW0II_Omo4Od3wFsTFhdDUWVrbc4YwMG6YOjbjgT33VEP5p8EVJ9sbjw)

  

![](https://lh7-us.googleusercontent.com/i3znBfS7NQCppkPaLCORbJ7TE75UmUOqkNDRInKf_XMsWKY5hzeA2tHpy7bLMi_55dSzCkj03es7qD_21Bd08eq9AyFhkyvYjg-AWP46h5o8QAjwme231V1kNcH64DeXFSpMAyxQ4ChuhoKJx6gzpm8)

  

![](https://lh7-us.googleusercontent.com/MooTnCIEtwCKQIq2xiza5eIFZtpKawcToJ7ISIPZx4BXxYiRPQjeyJzDFDEbUiC6-5gFad4RMHuQp99C8JQ8qoPegrm0OYK5FHL_WgXVaqs7SmFNpxS_8pr5cqdGCEvdM8dFtQXo1hUsd7x2x_pt370)

### Taskist:04: SSRF Vulnerability leads to chaining access to the root directory.


**Description:** The application's file upload functionality is vulnerable to Server-Side Request Forgery (SSRF), allowing attackers to initiate requests to arbitrary URLs, including local files such as ‘file:///’.

  

**Impact:** Attackers can exploit this vulnerability to read sensitive files on the server, such as /root/flag.txt, leading to unauthorized disclosure of sensitive information.

  

**Recommendation:** Implement strict input validation and filtering mechanisms to prevent SSRF attacks, and restrict file upload functionality to trusted sources only.

  

**Note:** The Taskist-4 should have been deployed at the user level to gain chain further to get flag{} at root level. Maybe it's not a bug but a Feature Who Knows what kind of rabbit hole it might be misleading us with other things.

  

**POC:**

  

- Send a Request to “file:///etc/” to know whether we are able to access the root files, after conclusion we are able to access the root directory files. Send request for “file:///root/flag.txt”
    

  

![](https://lh7-us.googleusercontent.com/7Mi0bSPRQNuhRLMZoKRROJAcxy6J70TaLvHIEee6V-e1vqkGBmsdV2-kqPW8QDX20DhPo486R3a7Xf_Txpa5jPwVTAp3WPgytTLlu7r-BjDaVdfCxjNVpNhSFmbxl43oPrPsvH4Qk94LA95Tce_RGzE)

---