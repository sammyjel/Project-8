# Project-8

##  configure apache as a load balancer
Let's put in place a load-balancing web solution for our DevOps team after the installation of the web servers in project 7.

Create an Ubuntu Server 20.04 EC2 instance and name it Project-8-apache-lb. Open TCP port 80 on Project-8-apache-lb by creating an Inbound Rule in the Security Group.

SSH into the Project-8-apache-lb instance and install Apache Load Balancer using the following commands:

`sudo apt update`
`sudo apt install apache2 -y`
`sudo apt-get install libxml2-dev`

Enable the required Apache modules using the following commands:

`sudo a2enmod rewrite`
`sudo a2enmod proxy`
`sudo a2enmod proxy_balancer`
`sudo a2enmod proxy_http`
`sudo a2enmod headers`
`sudo a2enmod lbmethod_bytraffic`

Restart the Apache service using the following command:

`sudo systemctl restart apache2`

Edit the Apache virtual host configuration file using the following command:

`sudo vi /etc/apache2/sites-available/000-default.conf`

Add the following configuration inside the <VirtualHost *:80> section:

<Proxy "balancer://mycluster">
  BalancerMember http://<WebServer1-Private-IP-Address>:80 loadfactor=5 timeout=1
  BalancerMember http://<WebServer2-Private-IP-Address>:80 loadfactor=5 timeout=1
  ProxySet lbmethod=bytraffic
  # ProxySet lbmethod=byrequests
</Proxy>
ProxyPreserveHost On
ProxyPass / balancer://mycluster/
ProxyPassReverse / balancer://mycluster/

Note: Replace <WebServer1-Private-IP-Address> and <WebServer2-Private-IP-Address> with the private IP addresses of your web servers.

Save and exit the configuration file.

Restart the Apache service using the following command:

`sudo systemctl restart apache2`

Verify that the load balancing configuration works by accessing the public IP address or Public DNS name of the load balancer from a browser:

`http://<Load-Balancer-Public-IP-Address-or-Public-DNS-Name>/index.php`

Replace <Load-Balancer-Public-IP-Address-or-Public-DNS-Name> with the public IP address or Public DNS name of your load balancer.

![mainpg](./images/Screenshot%202023-04-20%20150604.png)

Refresh the browser page several times and ensure that both web servers receive HTTP GET requests from the load balancer. New records should appear in each server's access log file.

use `cat /var/log/apache2/access.log` to view the access log and verify that they different HTTP GET requests. you should see the image below from the servers:

WBserver1 access log
![web1web2](./images/cat%20f%20access_log.png)  

WBserver2 access log
![web2](./images/wb2%20with%20higher%20files.png)

Here it shoows that WBserver 2 takes more traffic than WBserver 1 from load balanver. cogratulations!








