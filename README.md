# DFD4043 - Cloud Computing Part 1
## Stateless webapp deployment with EC2, S3, and Amplify

### Scenario:
> A company is searching for possible web developer for its corporate website. You are
required to provide a website prototype using the AWS cloud environment to a client. Work in a team of three to four people.

## 1. The Hard way with EC2 instance
> We will deploy an EC2 instance as a standalone static website.

1. OS: Amazon Linux 2
2. Webserver: Apache

> This website will utilize formspree.io to make form submission works on a client side
![Creative Agency](./images/StaticSiteForm1.png 'Creative Agency')

</br>

> The Final product Deployed with EC2 instance
![Creative Agency](./images/CreativeAgencyPic1.png 'Creative Agency')

> Making it secure with ssl/tls

1. Download epel repo
```bash
[ec2-user ~]$ sudo wget -r --no-parent -A 'epel-release-*.rpm' https://dl.fedoraproject.org/pub/epel/7/x86_64/Packages/e/

```
2. Install Epel Repo
```bash
sudo rpm -Uvh dl.fedoraproject.org/pub/epel/7/x86_64/Packages/e/epel-release-*.rpm
```
3. Enable Epel Repo
```
sudo yum-config-manager --enable epel*
```
4. Confirm the repo is available
```bash
sudo yum repolist all
```
5. Edit Apache config file
```bash
sudo vi /etc/httpd/conf.d/vhost.conf

# Insert config below
<VirtualHost *:80>
    DocumentRoot "/var/www/html"
    ServerName "example.com"
    ServerAlias "www.example.com"
</VirtualHost>
```
6. Save file and restart Apache
```bash
sudo systemctl restart httpd
```

> Install and run Certbot
```bash
sudo yum install -y certbot python2-certbot-apache
# run certbot
sudo certbot
```

> Apache Config Created by Certbot
```bash
Created an SSL vhost at /etc/httpd/conf.d/vhost-le-ssl.conf
Deploying Certificate to VirtualHost /etc/httpd/conf.d/vhost-le-ssl.conf
Redirecting vhost in /etc/httpd/conf.d/vhost.conf to ssl vhost in /etc/httpd/conf.d/vhost-le-ssl.conf
```
> References

[SSL/TLS on Amazon EC2](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/SSL-on-amazon-linux-2.html)

## 1.1 Moving to production with ALB and ASG

1. Creating Security Group for ALB
2. Creating ALB
3. Create Launch Template with provided shell scrip

### Shell Script for User Data
```bash
#!/usr/bin/env bash

########################################
##### USE THIS WITH AMAZON LINUX 2 #####
########################################

# install httpd (Linux 2 version)
yum update -y
yum install -y httpd git
cd /var/www/html
git clone https://github.com/zyzyx03/creative-agency-static-s3.git .
find . -type d -exec chmod 755 {} \;
find . -type f -exec chmod 644 {} \;
chown -R apache:apache .
systemctl start httpd.service
systemctl enable httpd.service
```

> ALG scaliblity test with Cloudwatch and 

```bash
# Load Testing with stress utility
amazon-linux-extras install epel -y
yum install stress -y
```


## 2. Another way with S3 Bucket

> Grant Access to S3 bucket with policy
```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "PublicReadGetObject",
            "Effect": "Allow",
            "Principal": "*",
            "Action": [
                "s3:GetObject"
            ],
            "Resource": [
                "arn:aws:s3:::Bucket-Name/*"
            ]
        }
    ]
}
```



## 3. Preferred way with AWS Amplify

> Hosting A static site with AWS Amplify
![Creative Agency](./images/AwsAmplify1.png 'Creative Agency')

> Download free html5 template

[Free HTML 5 template]("https://html5up.net")