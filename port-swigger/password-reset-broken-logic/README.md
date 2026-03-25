# Password Reset Broken Logic

## Overview

This lab demonstrates a broken password reset implementation where the application fails to properly validate user identity. By manipulating request parameters, an attacker can reset another user’s password and gain unauthorized access.

---

## Objective

* Exploit password reset logic flaw
* Reset the password of carlos
* Log in to the victim’s account

---

## Vulnerability Summary

The application:

* Trusts user-controlled input during password reset
* Does not verify ownership of the reset request
* Fails to bind the reset process to a specific user

This leads to **account takeover via parameter manipulation**.

---

## Lab Setup

* Tool: Burp Suite Community Edition
* Target: Web Security Academy
* Users:

  * wiener (attacker)
  * carlos (victim)

---

## Exploitation Steps

### 1. Initiate Password Reset

* Go to “Forgot Password”
* Enter username: wiener
* Go to the email for reset link
* Reset the password

<img width="960" height="477" alt="Forget password" src="https://github.com/user-attachments/assets/4535e6a0-1fe6-48fe-b938-6137567f70ec" /><BR><BR>

<img width="960" height="479" alt="Forget password for wiener" src="https://github.com/user-attachments/assets/439346d5-af5f-4de7-b111-1249748d83f9" /><BR><BR>

<img width="960" height="479" alt="link for password reset" src="https://github.com/user-attachments/assets/bad72a3d-7058-4d26-91a4-54c0b1a33781" /><BR><BR>

<img width="960" height="509" alt="reset password" src="https://github.com/user-attachments/assets/03e904c2-b025-4c99-9c41-085081281ec1" /><BR><BR>

<img width="960" height="474" alt="new password" src="https://github.com/user-attachments/assets/a23f56a9-1e92-48d1-97ba-ba71d6253d76" /><BR><BR>

---

### 2. Intercept Request

Capture the request in Burp Proxy and send it to Repeater:

<img width="960" height="477" alt="HTTP History" src="https://github.com/user-attachments/assets/f16387fa-c48a-44a7-94a2-d755c0278eb2" />

---

### 3. Observe Reset Flow

During the reset process, identify a request like: POST /forgot-password?temp-forgot-password-token

<img width="965" height="477" alt="remove the token and change the username" src="https://github.com/user-attachments/assets/ecfd8cf1-ec21-497f-aca7-b92ef6b9da6c" />

---

### 4. Modify the Request

Change the username and password to carlos and ben, Along with that delete the token from header and body.

<img width="960" height="479" alt="changed the password" src="https://github.com/user-attachments/assets/c8897d06-989a-4176-ae43-a153554681ff" />


---

### 5. Send Request

Forward the modified request in Repeater

Result:
The password for carlos is reset

<img width="960" height="477" alt="302 found" src="https://github.com/user-attachments/assets/d116257c-25f1-460d-9ce3-aa8e69b831bc" />

---

### 6. Access Victim Account

* Login using changed parameters

<img width="960" height="479" alt="logged in succesfully" src="https://github.com/user-attachments/assets/db7b36ab-6841-4f3b-8c07-274e78152206" />

---

## Root Cause

### Issue

* Application trusts client-controlled `username` parameter
* No validation of reset request ownership
* No secure mapping between reset request and user

---

### Expected Behavior

* Use secure reset tokens
* Bind tokens to specific users
* Validate requests server-side
* Do not rely on client input for identity

---

## Impact

* Account takeover
* Unauthorized password changes
* Exposure of sensitive data

---

## Remediation

* Enforce strict token validation
* Reject empty or invalid tokens
* Bind tokens to specific users
* Implement token expiration and single-use policy
* Perform all validation server-side

---

## Key Learning

* Security mechanisms must be enforced, not just implemented
* Tokens without validation are useless
* Password reset flows are critical attack surfaces
* Logic flaws can completely break authentication

---

## Questions & Answers

Q.1 Do I need valid credentials to exploit this?

No. The attack works through the password reset functionality.

---

Q.2 What is the main vulnerable parameter?

The `username` parameter in the reset request.

---

Q.3  Why does changing `wiener` to `carlos` work?

Because the application does not validate whether the reset request belongs to that user.

---

Q.4 What should the application use instead?

A secure reset token mapped to a specific user.

---

Q.5 What is the vulnerability type?

Broken Authentication / Business Logic Flaw

---

## One-line summary

The application allows password reset without validating the reset token, enabling account takeover.

## Conclusion

This lab highlights how failing to validate password reset tokens can completely break authentication security. Even though a token is generated, the lack of enforcement allows attackers to reset any user’s password.
