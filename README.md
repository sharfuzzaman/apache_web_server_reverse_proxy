##Apache web server reverse proxy configuration

### What is web server
   - A webserver is a software/hardware that runs web applications. The webserver's one of the main responsibilities is to store, process and deliver web requests and responds to the expected users.
   - In our case we are going to use apache webserver for achieve our reverse proxy.
### What is reverse proxy
A reverse proxy is a server that sits in front of web servers and forwards client (e.g. web browser) requests to those web servers
### Install Apache web server


At first, we are going to install apache2 webserver for the linux distribution (Debian based).

We have to first update our debian repository 
```bash
  $ sudo apt update
```
Then we will install apache2 webserver in our system
```bash
  $ sudo apt install apache2
```
After installing apache2 server check the status with
```bash
  $ sudo service apache2 status
```
The response will look like this


```javascript
● apache2.service - The Apache HTTP Server 

     Loaded: loaded (/lib/systemd/system/apache2.service; enabled; vendor prese> 

     Active: active (running) since Mon 2022-11-14 10:15:40 UTC; 11s ago 

       Docs: https://httpd.apache.org/docs/2.4/ 

    Process: 82543 ExecStart=/usr/sbin/apachectl start (code=exited, status=0/S> 

   Main PID: 82547 (apache2) 

      Tasks: 2 (limit: 614) 

     Memory: 17.0M 

     CGroup: /system.slice/apache2.service 

             ├─82547 /usr/sbin/apache2 -k start 

             └─82548 /usr/sbin/apache2 -k start 

  

Nov 14 10:15:39 dev.quickly systemd[1]: Starting The Apache HTTP Server... 

Nov 14 10:15:40 dev.quickly systemd[1]: Started The Apache HTTP Server. 
```
If, your reponse is not look this the start your apache webserver

```bash
   $ sudo service apache2 start
```

After, start your webserver please check the status again. If the status is active then you can look forward to the next stages. Oterwise, please uninstall the webserver and install it again.
##### Enable couple of modules for performing reverse Proxy
```bash
   $ sudo a2enmod proxy 
   $ sudo a2enmod proxy_http 
```
Then, reload the server for trach this changes
```bash
   $ sudo service apache2 reload
```
### Create virtual host for the application

Open apache web server default config file through your favorite editor

I am opening this file with vim editor Usually default conf file location is '/etc/apache2/sites-available/000-default.conf'
```bash
$ sudo vim /etc/apache2/sites-available/000-default.conf
```
After open this file please paste the below configuration
```javascript
 <VirtualHost *:80> 
   ServerName sajib.tk 
   DocumentRoot /home/application_path 

      <Directory /> 
         Options -Indexes +FollowSymlinks 
         AllowOverride None 
         Require all granted 
      </Directory>

        ProxyRequests Off 
        ProxyPreserveHost On 
        ProxyVia Full

      <Proxy *> 
         Require all granted 
      </Proxy>

      <Location /> 
         ProxyPass http://127.0.0.1:8080/ 
         ProxyPassReverse http://127.0.0.1:8080/ 
      </Location>

   ErrorLog ${APACHE_LOG_DIR}/error.log 
   CustomLog ${APACHE_LOG_DIR}/access.log combined 
</VirtualHost> 
``` 
   - Here 'http://127.0.0.1:8080' In port 8080 my application is running, in this case your application could run on a different port. Make sure your application running port is there.
   - ServerName here is sajib.tk (Please put your domain name here.)
Now restart your apache webserver, 
```bash
   $ sudo service apache2 restart
```
Now, open any browser and search with your given domain name. Your application should be seen by the users.


