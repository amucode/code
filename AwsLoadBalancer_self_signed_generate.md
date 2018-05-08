# 1: Generate private key

  openssl genrsa -des3 -out my_domain.key 1024


# 2: Generate CSR

  openssl req -nodes -newkey rsa:2048 -keyout my_domain.key -out my_domain.csr

3: Remove pass phrase from key

Make sure key only readable by root!

cp my_domain.key my_domain.key.org
openssl rsa -in my_domain.key.org -out my_domain.key
4: Generate certificate

openssl x509 -req -days 365 -in my_domain.csr -signkey my_domain.key -out my_domain.crt
5: Get Elastic IP for Load Balancer

6: Create Load Balancer

Go to AWS Control Panel -> EC2 Management Console -> Load Balancers
Create Instance
Set it to HTTPS (left-hand dropdown)
Leave it talking to EC2 instances on Port 80
Continue…
Choose Upload SSL Certificate
Display key text in terminal window:
openssl rsa -in my_domain.key -text

Copy that (Mac copy from terminal), including “Begin…End” sections; paste into text field in AWS console
Do the same with the certificate:
openssl x509 -inform PEM -in my_domain.crt

Several layers of saving…
7: Tidy up

Assign EC2 instances to ELB
Remove any elastic IPs from instances (so users can’t hit them directly)
Look at this AWS page – it’ll tell you how to set up a CNAME record in your DNS settings, to alias your domain subdomain to the ELB.
ELB doesn’t work with Elastic IPs – that’s because Amazon dynamically distribute the service over any number of machines.
So you set up a CNAME alias from sub.domain.com to my-loadbalancername-123456789.eu-west-1.elb.amazonaws.com
