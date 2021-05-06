# Ansible HAProxy Setup

Configure HAProxy and Apache Server over AWS RedHat using Ansible Roles and Ansible Plugins

# Requirements

To launch instances over AWS, we need `boto` and `boto3` library to be installed in system.

```sh
$ pip3 install --upgrade pip #to update pip in your system

$ pip install boto boto3
```

You also need to provide AWS credential so that Ansible fetch the account details. For this, create one IAM account in AWS.
For providing AWS credential, set the **environment variables** for your Secret and Access key:

```sh
$ export AWS_ACCESS_KEY_ID='YOUR_AWS_API_KEY'

$ export AWS_SECRET_ACCESS_KEY='YOUR_AWS_API_SECRET_KEY'
```

Now Install Ansible in your system using **pip** command, so that Ansible is installed with python3.

```sh
$ pip install ansible
```

# Usage

After follow above steps, Clone this repo in dir `/etc/ansible`...

```sh
$ git clone https://github.com/gaurav-gupta-gtm/ansible_haproxy.git /etc/ansible
```

# Ansible Roles

**1.** **ec2_host:**

   This role create key-pair in AWS and stored it in local path, created different security group over AWS for both loadbalancer & webserver, and launches EC2 instances over AWS tagged with webserver and lbserver.

**2.** **web:**

   This role configure HTTPD server over the instances which we launch using ec2_host role tagged as webserver.

**3.** **lbserver:**
   
   This ansible role helps in setup loadbalancer over EC2 instance tagged lbserver which we launched using ec2_host. This dynamically configure the loadbalancer config file and copy it to destination OS. 
   
# Ansible Plugins:

To setup dynamic Inventory `hosts/main_aws_ec2.yml` over AWS, it uses plugin `amazon.aws.aws_ec2`. This dynamically fetch Instance Over AWS and provide tag to them which we set.

```yaml
plugin: amazon.aws.aws_ec2
regions:
  - ap-south-1
keyed_groups:
  # add hosts to tag_Name_value groups for each aws_ec2 host's tags.Name variable
  - key: tags.Name
    prefix: tag_Name_
    separator: ""
hostnames:
  - ip-address
```

# Playbook

* lb.yml
```yaml
- hosts: localhost
  roles:
          - ec2_host
- hosts: tag_Name_webserver
  remote_user: ec2-user
  roles:
          - web

- hosts: tag_Name_lbserver
  remote_user: ec2-user
  roles:
          - lbserver
```
  
Run this playbook for configure whole setup:

```sh
$ ansible-playbook lb.yml
```

To know more, visit the blog: https://techq.medium.com/how-to-deploy-haproxy-loadbalancer-over-aws-in-a-dynamic-way-using-ansible-plugins-a5ce7eda2495
