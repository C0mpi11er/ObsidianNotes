what are passwords?
Passwords are used as an authentication method for individuals to access computer systems or applications. Using passwords ensures the owner of the account is the only one who has access. However, if the password is shared or falls into the wrong hands, unauthorized changes to a given system could occur. Unauthorized access could potentially lead to changes in the system's overall status and health or damage the file system. Passwords are typically comprised of a combination of characters such as letters, numbers, and symbols. Thus, it is up to the user how they generate passwords!

### Password Cracking vs. Password Guessing


- Password guessing is a technique used to target online protocols and services. Therefore, it's considered time-consuming and opens up the opportunity to generate logs for the failed login attempts. A password guessing attack conducted on a web-based system often requires a new request to be sent for each attempt, which can be easily detected. It may cause an account to be locked out if the system is designed and configured securely.
- Password cracking is a technique performed locally or on systems controlled by the attacker.

Here are some website lists that provide default passwords for various products.

- [https://cirt.net/passwords](https://cirt.net/passwords)
- [https://default-password.info/](https://default-password.info/)
- [https://datarecovery.com/rd/default-passwords/](https://datarecovery.com/rd/default-passwords/)

- [https://www.skullsecurity.org/wiki/Passwords](https://www.skullsecurity.org/wiki/Passwords) - This includes the most well-known collections of passwords.
- [SecLists](https://github.com/danielmiessler/SecLists/tree/master/Passwords) - A huge collection of all kinds of lists, not only for password cracking.


Tools such as Cewl can be used to effectively crawl a website and extract strings or keywords. Cewl is a powerful tool to generate a wordlist specific to a given company or target. Consider the following example below:


cewl  -w list.txt -d 5 -m 5 url.com



Thankfully, there is a tool "username_generator" that could help create a list with most of the possible combinations if we have a first name and last name.

echo "john smith" > list.txt
python3 username_generator -w list.txt