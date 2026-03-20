# Username Enumeration via Different Responses

**Platform:** PortSwigger Web Security Academy<BR>
**Category:** Authentication<BR>
**Difficulty:** Apprentice<BR>

---

## Lab Objective

The goal of this lab is to identify a valid username by analyzing differences in server responses during login attempts, and then use this information to brute-force the password.

---

## Vulnerability Overview

Username enumeration occurs when an application reveals whether a username exists based on differences in login responses.

Example:

* Invalid username → "Invalid username"
* Valid username with incorrect password → "Incorrect password"

This behavior allows attackers to identify valid usernames and perform targeted brute-force attacks.

---

## Tools Used

* Burp Suite Community Edition

---

## Step 1: Access Login Page

Access the login functionality of the application, for that give a random username and password.

<img width="1920" height="987" alt="login" src="https://github.com/user-attachments/assets/21cd75f3-645b-499b-9668-f34f03f72229" />


---

## Step 2: Intercept Request

Use Burp Suite to intercept the login request.

<img width="1920" height="977" alt="login request to intruder" src="https://github.com/user-attachments/assets/2dd3e17d-90cb-4994-b0fd-2025291c4869" />



---

## Step 3: Username Enumeration

1. Send the request to Burp Intruder
2. Select the `username` here kevin, parameter as the payload position which then shows as $username$
3. Use a username wordlist , thats already given in the port swigger portal, copy paste in the payload section.
4. Start the attack

Analyze the responses:

* Most responses return "Invalid username"
* One response returns "Incorrect password"

This indicates a valid username. Below it shows ongoing attack with the payload.

<img width="1920" height="982" alt="User name Attack" src="https://github.com/user-attachments/assets/a195e5d4-3089-4fec-afda-d5c91618561c" />
<BR>
<BR>
Here we can see the difference in the length of response, BINGO!!! thats how we know the correct username .<BR>
ie; for all the incorrect username the lenghth is same here 3352 and shows "Incorrect username"<BR>
where as for one payload "ansible" the length is 3354 and shows "Incorrect password" which means there is user named "ansible"<BR>
(BTW length is byte length)<BR>
<BR><BR>
<img width="1920" height="982" alt="Responce difference" src="https://github.com/user-attachments/assets/9a0c24c5-1352-4566-9dc6-fd9f9684b15e" />
<BR><BR>
<img width="1920" height="981" alt="Another responce" src="https://github.com/user-attachments/assets/464ddb64-2887-491d-a9a1-578fda50bc91" />

---

## Step 4: Password Brute Force

1. Fix the identified valid username, ie; change the random username kevin to ansible
2. Send the request to Intruder again
3. Target the `password` parameter ie; Add the password parameter $kevin123$
4. Use a password wordlist

<img width="1920" height="979" alt="password select for attack" src="https://github.com/user-attachments/assets/7da5071d-aead-4ee7-b2ba-3cf713c93dbb" />
<BR><BR>Just like username we get the password by looking into the lenght of the response and content it clearly takes us to the password, 
which here is taylor
<BR><BR>
<img width="1920" height="982" alt="Password attack" src="https://github.com/user-attachments/assets/710dcecf-6559-4188-989d-2b88794374bd" />
<BR><BR>
<img width="1920" height="991" alt="Less responce" src="https://github.com/user-attachments/assets/596868c8-06ad-4edc-8599-8426e33c34ad" />
<BR><BR>

---

## Step 5: Successful Login

Use the identified credentials to log in successfully. log in normally in the webpage using <BR><BR>
username:ansible <BR>
password:taylor <BR><BR>

<img width="1920" height="986" alt="Successful login" src="https://github.com/user-attachments/assets/2e8a6ad9-d8cd-4a8d-8356-0de81435be74" />

<BR><BR>

---

## Impact

* Enables attackers to identify valid usernames
* Facilitates targeted brute-force attacks
* Increases risk of unauthorized access

---

## Mitigation

* Use generic <B>error messages </B> such as "Invalid username or password"
* Implement <B>rate limiting</B>
* Enforce <B>account lockout </B>policies
* Use <B>CAPTCHA</B> after repeated failed attempts

---

## Key Takeaway

Authentication mechanisms should not disclose whether a <B>username exists</B>, as this significantly reduces the effort required for attackers to compromise accounts.

---
