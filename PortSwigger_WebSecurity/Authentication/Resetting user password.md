In practice some users will forget their password, so it is common to have a way for them to reset it. As the usual password-based authentication is obviously impossible in this scenario, websites have to rely on alternative methods to make sure that the real user is resetting their own password. For this reason, the password reset functionality is inherently dangerous and needs to be implemented securely.

There are a few different ways that this feature is commonly implemented, with varying degrees of vulnerability.

->>sometimes the url shows a name of the user password being reset changin that can change the user...and reset another user password

->>failure to validate reset form token(cookies) can be exploitable ....check cookies or map how the process work ti find vulnerablility sometimes names change or no cookie validation