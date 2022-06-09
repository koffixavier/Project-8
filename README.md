# Project-8
Load Balancer Solution With Apache
## Step 1: Configure architecture from project 7

![Screen Shot 2022-05-31 at 5 57 07 PM](https://user-images.githubusercontent.com/101080172/172284629-3f33bade-afa3-4de2-bfbe-7c04cff13228.png)

## Step 2: Configure Apache as a Load Balancer

![Screen Shot 2022-05-31 at 6 26 06 PM](https://user-images.githubusercontent.com/101080172/172285001-b0057747-d8c8-4045-b1df-7c97529b3a9e.png)

- Open TCP 80 on the instance's security group

- Install Apache

```
 sudo apt update
 sudo apt install apache2 -y
 sudo apt-get install libxml2-dev
```

![Screen Shot 2022-05-31 at 5 35 29 PM](https://user-images.githubusercontent.com/101080172/172285240-8c457456-eb80-4526-a453-db3079c0dde3.png)

![Screen Shot 2022-05-31 at 5 37 38 PM](https://user-images.githubusercontent.com/101080172/172285250-bc41ecd6-0339-49ec-88aa-33f5f33a4c2e.png)

- Enable the following modules

```
sudo a2enmod rewrite
sudo a2enmod proxy
sudo a2enmod proxy_balancer
sudo a2enmod proxy_http
sudo a2enmod headers
sudo a2enmod lbmethod_bytraffic
```

- Restart Apache

```
sudo systemctl restart apache2
```

![Screen Shot 2022-05-31 at 5 42 14 PM](https://user-images.githubusercontent.com/101080172/172285263-6857a119-7762-4123-a3f9-1f0cfaffe6d1.png)


![Screen Shot 2022-05-31 at 6 18 26 PM](https://user-images.githubusercontent.com/101080172/172284156-67e5ed9d-5513-496a-b6bb-5982ebd0a8d0.png)

- Configure load balancing

```
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
```
- Restart apache server

![Screen Shot 2022-05-31 at 6 18 48 PM](https://user-images.githubusercontent.com/101080172/172284165-abbd166c-ab2b-4853-a9e3-92dcccd9ef71.png)
![Screen Shot 2022-05-31 at 6 22 26 PM](https://user-images.githubusercontent.com/101080172/172284180-1e46e083-4af5-4446-98e9-49d6d2f962b7.png)

- Verify the configuration works

```
Run sudo tail -f /var/log/httpd/access_log on both servers and check the outputs (refresh your browser a couple or more times)
```
![Screen Shot 2022-05-31 at 6 05 52 PM](https://user-images.githubusercontent.com/101080172/172284116-8f8bdb64-ee54-433b-af52-f6de86f9c41e.png)
![Screen Shot 2022-05-31 at 6 11 52 PM](https://user-images.githubusercontent.com/101080172/172284136-6f139e9c-74a8-4356-adec-5a6f18edd42e.png)
![Screen Shot 2022-05-31 at 6 12 08 PM](https://user-images.githubusercontent.com/101080172/172284146-9cfbb9bf-e1ce-42fb-a246-73d731ea6872.png)

## Configure local DNS Resolution
- Edit your hosts file
```
sudo vim /etc/hosts

Add the following line:

    <WebServer1-Private-IP-Address> Web1
    <WebServer2-Private-IP-Address> Web2
```


![Screen Shot 2022-05-31 at 6 18 26 PM](https://user-images.githubusercontent.com/101080172/172284156-67e5ed9d-5513-496a-b6bb-5982ebd0a8d0.png)

- Edit the /etc/sites-available/000-default.conf file

```
BalancerMember http://Web1:80 loadfactor=5 timeout=1
BalancerMember http://Web2:80 loadfactor=5 timeout=1
```
![Screen Shot 2022-06-08 at 8 47 43 PM](https://user-images.githubusercontent.com/101080172/172741090-bcdea06e-c96a-4509-baf8-6b6b0c9a4eb8.png)

- curl your Web Servers from LB locally curl http://Web1 or curl http://Web2

![Screen Shot 2022-05-31 at 6 24 57 PM](https://user-images.githubusercontent.com/101080172/172284188-11de6496-ea3b-45d7-9bc2-a761d5464141.png)
![Screen Shot 2022-05-31 at 6 25 40 PM](https://user-images.githubusercontent.com/101080172/172284200-6de6576e-8ff1-4acf-a19e-b0cf5c3003aa.png)

