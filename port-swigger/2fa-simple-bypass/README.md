# 2FA Simple Bypass

**Platform:** PortSwigger Web Security Academy<BR>
**Category:** Authentication<BR>
**Difficulty:** Apprentice<BR>

---

## Lab Objective

Bypass two-factor authentication and access another user's account without completing the second authentication step.<BR>Here the second authentication is otp from mail.<BR> So basically we are tring to bypass <b>ONLY</B> with carlos's username and password. 

---

## Vulnerability Overview

The application does not properly enforce the 2FA verification step. After login, users can directly access authenticated endpoints without completing the second factor.<BR> ie; no 2nd factor is implemented.

---

## Tools Used

* Burp Suite Community Edition

---

## Step 1 — Login Page

Open the login page and enter the provided credentials.

<img width="960" height="525" alt="login with my id" src="https://github.com/user-attachments/assets/443db4a9-0274-4cc1-b9f3-d8cebf8cd3a2" />


---

## Step 2 — 2FA Verification Prompt

After successful login, the application redirects to a 2FA verification page asking for a code. 

<img width="960" height="476" alt="goto email client for code" src="https://github.com/user-attachments/assets/4dfa7206-7bf5-43f9-ad53-4249f7cd077f" /><BR><BR>
<img width="960" height="488" alt="logged in with code" src="https://github.com/user-attachments/assets/527987a6-57aa-486f-a7b1-45ae0c532c7e" />


---

## Step 3 — Intercept Request in Burp Suite

Capture the login/redirect flow using Burp Suite.

Observe how the application moves to a 2FA endpoint instead of completing authentication.

<img width="960" height="477" alt="send myaccount request to repeater" src="https://github.com/user-attachments/assets/ba3f138b-664a-4d25-bcd8-52bfdd57b671" />


---

## Step 4 — Attempt Direct Access

Without entering the 2FA code, manually access a sensitive endpoint. <BR>

https://0a58008b04b9c120800b805500cc00f5.web-security-academy.net/login2<BR>

Above is the url of login reguest , if we look into the repeater we can see the previous one had /my-account?id=wiener , we can change it and try<BR><BR>
Example:

/my-account?id=carlos<BR>

<img width="960" height="476" alt="change login to myaccount" src="https://github.com/user-attachments/assets/660f7b90-546b-47d5-8a47-cf3157629c09" />

---

## Step 5 — Successful Bypass

The application grants access to the account without requiring 2FA verification.<BR>

<img width="960" height="462" alt="succsess" src="https://github.com/user-attachments/assets/b11a6345-e211-483a-905e-10bcbb88066e" />


---

## Impact

* Two-factor authentication can be bypassed
* Unauthorized access to user accounts
* Critical authentication flaw

---

## Mitigation

* Enforce 2FA validation before granting access
* Bind session to completed 2FA verification
* Restrict access to endpoints until verification is complete

---

## Key Takeaway

<B>2FA is only effective if strictly enforced. Any bypass completely breaks the authentication mechanism.</B>

---
Questions & Answers (Learning Notes)
1. Do I need to complete the full login process (including 2FA) to exploit this vulnerability?

Answer:
No. The application grants a valid session after verifying credentials, which can be used without completing 2FA.

2. Why does changing the id parameter to carlos work?

Answer:
Because the application does not validate if the session belongs to the requested user.

3. Is the vulnerability due to URL manipulation?

Answer:
No. The root issue is broken authentication and authorization logic. URL manipulation is just the exploitation method.

4.What role does the session cookie play?

Answer:
It represents an authenticated session, which is wrongly treated as fully verified.

5. Is this an authentication issue or an authorization issue?

Answer:
Primarily authentication (2FA bypass), but it leads to authorization failure.

6. What is the core vulnerability in one line?

Answer:
The application grants full access after password verification without enforcing 2FA.
