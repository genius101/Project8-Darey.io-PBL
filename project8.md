<h1> In this project, we will enhance our Tooling Website solution by adding a Load Balancer</h1>

<p>The Load balancer would be used to disctribute traffic between Web Servers and allow users to access our website using a single URL.</p>

![Tooling-Website-Infrastructure-wLB](https://user-images.githubusercontent.com/10243139/124442850-0df4ef00-dd75-11eb-9794-396463c01bd5.png)

<h2>Prerequisites</h2>

<p>Make sure that you have following servers installed and configured within Project-7:</p>

<p>Link to project 7: https://github.com/genius101/Project7-Darey.io-PBL/blob/main/project7.md</p>

- [ ] Two RHEL8 Web Servers
- [ ] One MySQL DB Server (based on Ubuntu 20.04)
- [ ] One RHEL8 NFS server

![prerequisites-project8](https://user-images.githubusercontent.com/10243139/124443659-c753c480-dd75-11eb-81eb-9809cb1e8ce2.png)

<h2>Step 1 - Configure Apache As A Load Balancer</h2>

<p>Spin up an EC2 instance in Ubuntu and name it Project-8-apache-lb</p>

<p>so your EC2 list will look like this:</p>

![project8_ec2](https://user-images.githubusercontent.com/10243139/124444171-3cbf9500-dd76-11eb-8b73-2c1a2f039f70.png)

<p>Open TCP port 80 on Project-8-apache-lb by creating an Inbound Rule in Security Group</p>

<p>Install Apache Load Balancer on Project-8-apache-lb server and configure it to point traffic coming to LB to both Web Servers:</p>

    #Install apache2
    sudo apt update
    sudo apt install apache2 -y
    sudo apt-get install libxml2-dev

    #Enable following modules:
    sudo a2enmod rewrite
    sudo a2enmod proxy
    sudo a2enmod proxy_balancer
    sudo a2enmod proxy_http
    sudo a2enmod headers
    sudo a2enmod lbmethod_bytraffic

    #Restart apache2 service
    sudo systemctl restart apache2
    
<p>Make sure apache2 is up and running:</p>
    
    sudo systemctl status apache2
    
<p>Configure load balancing:</p>

    sudo vi /etc/apache2/sites-available/000-default.conf

    #Add this configuration into this section <VirtualHost *:80>  </VirtualHost>

    <Proxy "balancer://mycluster">
                   BalancerMember http://<WebServer1-Private-IP-Address>:80 loadfactor=5 timeout=1
                   BalancerMember http://<WebServer2-Private-IP-Address>:80 loadfactor=5 timeout=1
                   ProxySet lbmethod=bytraffic
                   # ProxySet lbmethod=byrequests
            </Proxy>

            ProxyPreserveHost On
            ProxyPass / balancer://mycluster/
            ProxyPassReverse / balancer://mycluster/

    #Restart apache server

    sudo systemctl restart apache2
    
<p>Verify that our configuration works - try to access your LB’s public IP address or Public DNS name from your browser:</p>

	http://<lb-public-ipaddress>
  
![1 g](https://user-images.githubusercontent.com/10243139/124444905-f880c480-dd76-11eb-9b6d-c9c345b34b56.jpg)

<p>Now your set up looks like this:</p>

![project8_final](https://user-images.githubusercontent.com/10243139/124445002-10f0df00-dd77-11eb-81ae-08d48f69cae1.png)

<h3>Congratulations!</h3>

<h4>You have just implemented a Load balancing Web Solution for your DevOps team</h4>
