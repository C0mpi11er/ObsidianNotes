

->> sign in
-> sending profile fill in the blanks  name,from, host
name =fake name ,from=fake email,  host= 127.0.0.1:25

->> landing page
give it a name and paste html code corresponding 

->> email
This is the design and content of the email you're going to actually send to the victim; it will need to be persuasive and contain a link to your landing page to enable us to capture the victim's username and password. Click the **Email Templates** link on the left-hand menu and then click the **New Template** button. Give the template the name **Email 1**, the subject **New Message Received**, click the HTML tab, and then the Source button to enable HTML editor mode. In the contents write a persuasive email that would convince the user to click the link, the link text will need to be set to **[https://admin.acmeitsupport.thm](https://admin.acmeitsupport.thm)**, but the actual link will need to be set to **{{.URL}}** which will get changed to our spoofed landing page when the email gets sent, you can do this by highlighting the link text and then clicking the link button on the top row of icons, make sure to set the **protocol** dropdown to **<other>**.

![](https://tryhackme-images.s3.amazonaws.com/user-uploads/5efe36fb68daf465530ca761/room-content/5a41d611e4d01c56aa74ca5e7867138b.png)



->> user and groups 
This is where we can store the email addresses of our intended targets. Click the **Users & Groups** link on the left-hand menu and then click the **New Group** button. Give the group the name **Targets** and then add the following email addresses:


->> campaigns
 click campaign and set detailes date of launch should be 2 days ago and server should be http://<ip> the ip of listen server



# Droppers
Droppers are software that phishing victims tend to be tricked into downloading and running on their system. The dropper may advertise itself as something useful or legitimate such as a codec to view a certain video or software to open a specific file.

The droppers are not usually malicious themselves, so they tend to pass antivirus checks. Once installed, the intended malware is either unpacked or downloaded from a server and installed onto the victim's computer. The malicious software usually connects back to the attacker's infrastructure. The attacker can take control of the victim's computer, which can further explore and exploit the local network.