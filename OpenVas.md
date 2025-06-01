first install doceker
secondly run this command to get all the packaged and dependencies of openvas at once 
```shell-session
sudo docker run -d -p 443:443 --name openvas mikesplain/openvas
```



### Accessing OpenVAS

Once you have completed the installation process mentioned above, you can access the OpenVAS web interface by opening any of your browsers and typing the following in the URL:

`https://127.0.0.1`


if openvas already exist just open:
docker start openvas