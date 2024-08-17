Role Name: ad_join 
------------

Description: Automating the Process of Joining Linux Systems to an AD Domain using Ansible and SSSD
Requirements
------------

Any pre-requisites that may not be covered by Ansible itself or the role should be mentioned here.

To automate the process of joining Linux systems to an AD domain using SSSD, follow these steps:

- Install Ansible: Set up Ansible on a control node that will manage the configuration of your Linux systems.

[+] https://docs.ansible.com/ansible/latest/installation_guide/intro_installation.html

- Setup an Active Directory: You can as exemple AWS Directory service. You can refer to artical below or offical AWS doc.

[+] https://medium.com/@medkamel555/configure-aws-managed-microsoft-ad-active-directory-and-join-your-linux-ec2-instance-to-the-domain-71fc5a0afaee

[+] https://docs.aws.amazon.com/whitepapers/latest/active-directory-domain-services/directory-services-options-in-aws.html

- Define the Inventory: Create an inventory file that lists the Linux systems you want to join to the AD domain.

- Make sure that your servers are able to reslove the domain name. You can use `dig corp.exmple.com` to check that.

- Make sure that you can reach the domain name via LDAP port (389) and DNS port (53). You need to whitelist your outbound traffic to your AD server.

Role Variables
--------------

A description of the settable variables for this role should go here, including any variables that are in defaults/main.yml, vars/main.yml, and any variables that can/should be set via parameters to the role. Any variables that are read from other roles and/or the global scope (ie. hostvars, group vars, etc.) should be mentioned here as well.


You need to privode the details to join linux into domain, like domain user who has right to add client into domain and DNS server.
Create ad.yaml file with the content below and include it into the plyabook. Make sure /etc/resolv.conf contains the dns server address IP.

ad_server:
    user: admin
    pass: 'test'
    domain: corp.exmple.com


You have look to the inventory setup in the github link below:

[+] https://github.com/medkamel555/Ad_join

OS Linux Supported 
---------

- RHEL 7 
- RHEL 8
- RHEL 9
- Centos
- Amazon Linux 1
- Amazon Linux 2
- Amazon Linux 2023

Dependencies
------------

A list of other roles hosted on Galaxy should go here, plus any details in regards to parameters that may need to be set for other roles, or variables that are used from other roles.

Example Playbook
----------------

Clone the repository in the roles directory in ansible as install_snmpd and include this role in your list. For example


    - name: "Ad join"
      hosts: dataseserver
      roles:
        - ad_join


Testing
-------
- Confirm that the join was successfully done via the command below.

 ```
realm list
 ```
- You are able to SSH to the server using your ldap credentials. Exemple of command

```
ssh -l user@corp.exmple.com IPxxxxxxxxxx
```
The user can enter the username in either the username@example.com or EXAMPLE\username format. Then, you are able to connect the server with the specified user.

Futhermore, you can allows your to run programs with the security privileges if your user is part of AWS Delegated Administrators group in the AD


Integration
-------

TBD: Share galaxy command to add install the role in your project. 

License
-------

BSD

TODO:
------
Some actions are planned in the upcoming role version:

- SUSE and Ubuntu to be supported by the role.
- Tested on on-premises AD such as Microsoft Active Directory and do the necessary updates to be a more general role for all ADs.
- Support Windows join: Need code refactoring to support Windows server domain join.

[+] https://docs.ansible.com/ansible/latest/os_guide/windows_usage.html

Author Information
------------------
Mohamed Kamel CHHAYDER
Title: System Devops engineer  
Email: medkamel555@gmail.com
Linkedin: linkedin.com/in/chhayder
