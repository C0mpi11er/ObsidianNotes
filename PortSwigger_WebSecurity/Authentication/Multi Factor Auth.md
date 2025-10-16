## Bypassing two-factor authentication

At times, the implementation of two-factor authentication is flawed to the point where it can be bypassed entirely.

If the user is first prompted to enter a password, and then prompted to enter a verification code on a separate page, the user is effectively in a "logged in" state before they have entered the verification code. In this case, it is worth testing to see if you can directly skip to "logged-in only" pages after completing the first authentication step. Occasionally, you will find that a website doesn't actually check whether or not you completed the second step before loading the page.


## Flawed two-factor verification logic

Sometimes flawed logic in two-factor authentication means that after a user has completed the initial login step, the website doesn't adequately verify that the same user is completing the second step.

For example, the user logs in with their normal credentials in the first step as follows:

`POST /login-steps/first HTTP/1.1 Host: vulnerable-website.com ... username=carlos&password=qwerty`

They are then assigned a cookie that relates to their account, before being taken to the second step of the login process:

`HTTP/1.1 200 OK Set-Cookie: account=carlos GET /login-steps/second HTTP/1.1 Cookie: account=carlos`

When submitting the verification code, the request uses this cookie to determine which account the user is trying to access:

`POST /login-steps/second HTTP/1.1 Host: vulnerable-website.com Cookie: account=carlos ... verification-code=123456`

In this case, an attacker could log in using their own credentials but then change the value of the `account` cookie to any arbitrary username when submitting the verification code.

`POST /login-steps/second HTTP/1.1 Host: vulnerable-website.com Cookie: account=victim-user ... verification-code=123456`

This is extremely dangerous if the attacker is then able to brute-force the verification code as it would allow them to log in to arbitrary users' accounts based entirely on their username. They would never even need to know the user's password.

# Note 
always make sure to map the website verification process through the proxy https history and pay attention to request sent and recieved  
->> check cookie auth,brutforce eligibility in looking for mfa-code to be sure before proceeding to intruder 
pay more attention to the cookies 