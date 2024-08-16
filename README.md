domain_join
Automate Linux Machine join AD using Ansible. 


To automate the process of joining Linux systems to an AD domain using SSSD, follow these steps:



Install Ansible: Set up Ansible on a control node that will manage the configuration of your Linux systems.
Define the Inventory: Create an inventory file that lists the Linux systems you want to join to the AD domain.
2.4
Operating systems
Tested with RHEL 9 RHEL 8 Centos 7 Amazon Linux 1/2

Example Playbook
Clone the repository in the roles directory in ansible as install_snmpd and include this role in your list. For example

- host: all
  vars_files:
    - vars/ad.yaml
  roles:
    - domain_join

    
Variables
You need to privode the details to join linux into domain, like domain user who has right to add client into domain and DNS server and FQDNS.
Create ad.yaml file with the content below and include it into the plyabook. Make sure /etc/resolv.conf contains the dns server address
---
- ad_server:
    ip: xxxx
    fqdn: ad1.example.com
    user: svc_ad
    pass: 'xxxx'
    domain: example.com
Modify ou_membership to add the machine to custom OU

Testing
sudo realm list
id __user_name__
ssh __user_name__@localhost
Leaving the domain
If you want to reverse the process and remove yourself from the domain, simply run the ‘realm leave’ command followed by the domain name, as shown below.
realm leave example.com

OS Supported 

- RHEL 7 
- RHEL 8
- RHEL 9
- Centos
- Amazon Linux 1/2
- Amazon Linux 2023
