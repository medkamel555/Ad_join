Role Name: ad_join 
------------

Description: Automating the Process of Joining Linux Systems to an AD Domain using Ansible and SSSD
Requirements
------------

Any pre-requisites that may not be covered by Ansible itself or the role should be mentioned here.

- Setup an Active Directory: You can as an example use AWS Directory service. You can refer to the articles below or the official AWS documentation.

[Configure AWS Managed Microsoft AD Active Directory](https://medium.com/@medkamel555/configure-aws-managed-microsoft-ad-active-directory-and-join-your-linux-ec2-instance-to-the-domain-71fc5a0afaee)

[Directory services options in AWS](https://docs.aws.amazon.com/whitepapers/latest/active-directory-domain-services/directory-services-options-in-aws.html)

- Define the Inventory: Create an inventory file that lists the Linux systems you want to join to the AD domain.

- Make sure that your servers are able to resolve the domain name. You can use dig corp.example.com to check that.

- Make sure that you can reach the domain name via LDAP port (389) and DNS port (53). You need to whitelist your outbound traffic to your AD server.

Role Variables
--------------

You need to provide the details to join Linux into the domain, like the domain user who has the right to add clients into the domain and the DNS server. Create ad.yaml file with the content below and include it into the playbook. Make sure /etc/resolv.conf contains the DNS server address IP.

```yaml
- ad_server:
    user: admin
    pass: 'test'
    domain: corp.exmple.com
```

You can have a look at the example inventory setup in the GitHub link below:


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

 ```bash
realm list
 ```
- You are able to SSH to the server using your ldap credentials. Exemple of command

```bash
id __user_name__
```
```bash
ssh -l user@corp.exmple.com IPxxxxxxxxxx
```
The user can enter the username in either the username@example.com or EXAMPLE\username format. Then, you should be able to connect to the server with the specified user.
Furthermore, if your user is part of the AWS Delegated Administrators group in the AD, it allows you to run programs with the security privileges.

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
- Name: Mohamed Kamel CHHAYDER
- Title: System Devops engineer  
- Email: medkamel555@gmail.com
- Linkedin: linkedin.com/in/chhayder
