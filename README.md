# UA92 Project - Tattoo Website

##[Live website link](https://icybox.co.uk/)

My original idea for my project was to create a blog, using python and the django framework. I managed to successfully create the blog, however it was more about how it functions on the backend rather than concentrating on the UI/UX. As a result, I decided to not continue developing that project and decided to concentrate on a vanilla HTML and CSS website, focusing on the fundamentals.

## Sections
- [Design and Layout](#design-and-layout)
- [Technologies Used](#technologies-used)
- [GDPR](#gdpr)
- [CD/CD](#cicd)
- [YAML Template](#yaml-template)

---

## Design and Layout


The idea for my new website, is to create a new website for an imaginary tattoo shop. When I was researching other tattoo shop websites, the styles vary, but they all pretty much have the same content and webpages.

My website pages will be as follows;

- Home page
    - This page will be the index page and will have featured tattoo styles in a grid format
- Artists
    - This page will list all the tattoo artists with their pictures, social media and a bit of information about them
- Our Art
    - This will showcase all the tattoo styles the shop specialises in, in a grid format
- About
    - This will have the history of the tattoo shop
- Contact Us
    - This page will have a contact form for consultations or bookings. It will also have an embedded Google maps location of the studio

---

## Technologies Used

- HTML 5
- CSS
- AWS
- Linux/Ubuntu
- NGINX
- GitHub
- Cloudflare

Using Visual Studio Code, I wrote my website using only vanilla HTML5 and CSS. I wanted to demonstrate a good understanding of the fundamentals of web design without using any web frameworks. I am using AWS to host my website, using an Ubuntu image rather than Amazon Linux. I am more familiar with the Linux commands for Ubuntu. I am using NGINX for my webserver, using a custom configuration file. I am using GitHub for my version control and Cloudflare for my DNS.

---

## GDPR

Since my website is purely frontend, it does not retain any data, other than what is hard coded in the HTML files. As a result, there is no requirements to adhere to GDPR guidelines. However, I can still enable SSL/TLS certificates on Cloudflare to allow HTTPS traffic.

---

## CI/CD

Using a YAML template, I can utilise AWS CloudFormation. The YAML template creates a Linux Ubuntu server, using an existing security group to allow HTTP (port 80), HTTPS (port 443) and SSH (port 22) traffic. The YAML template also incorporates a BASH script which will do the following;

- Update the packages on the server
- Install NGINX
- Clone my NGINX configuration file from GitHub to a specific directory and unlink the existing default configuration file
- Clone all my website project files to a specific directory
- Reload NGINX

So as a result of this template, when I upload the YAML file using AWS CloudFormation all I need to do is complete the steps to complete the stack and wait for the process to complete. After a few minutes, I use the public IP address assigned to my virtual machine and my website will be accessible over the internet.

---

## YAML Template

```yaml
AWSTemplateFormatVersion: "2010-09-09"

Description: Tattoo Webserver Template

Metadata:
  AWS::CloudFormation::Interface:
    ParameterGroups:
      - Label:
          default: Amazon EC2 Configuration
        Parameters:
          - InstanceType
    ParameterLabels:
      InstanceType:
        default: Type of EC2 Instance

Parameters:
  InstanceType:
    Description: Enter t2.micro, t2.small. or t2.large Default is t2.micro.
    Type: String
    AllowedValues:
      - t2.micro
      - t2.small
      - t2.large	
    Default: t2.micro

Resources:
  tattooWebsite:
    Type: AWS::EC2::Instance
    Properties:
      ImageId: ami-053b0d53c279acc90 # Ubuntu image. US East
      KeyName: web-dev
      SecurityGroupIds:
        - sg-04082b317dabb6363 # default security group 22, 80 & 443
      InstanceType: !Ref InstanceType
      UserData:
        Fn::Base64: !Sub |
          #!/bin/bash
          apt update
          apt upgrade -y
          apt install nginx -y
          cd /etc/nginx/conf.d
          git clone https://github.com/icybox129/nginx-config.git .
          cd ..
          cd sites-enabled/
          unlink default 
          cd /var/www
          mkdir vhosts
          cd vhosts/
          mkdir test.icybox.co.uk
          cd test.icybox.co.uk/
          git clone https://github.com/icybox129/ua92-tattoo-webiste.git .
          systemctl reload nginx
      Tags:
       - Key: Name
         Value: test-webserver

Outputs:
  InstanceId:
    Value: !Ref tattooWebsite
```
