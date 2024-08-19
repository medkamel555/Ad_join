
## Joining Linux Systems to an AD Domain using Ansible

Description: Automating the Process of Joining Linux Systems to an AD Domain using Ansible and SSSD

To automate the process of joining Linux systems to an AD domain using SSSD, follow these steps below:

### 1 - Install needed dependencies

#### 1.1 Install Ansible 
on Ubuntu
```bash
sudo apt install ansible
```
on Centos 7
```bash
yum -y install ansible
```
on Centos 8
```bash
pip3 install ansible --user
```
On RHEL 7
```bash
sudo subscription-manager repos --enable rhel-7-server-ansible-2.9-rpms
yum -y install ansible
```
On RHEL 8
```bash
subscription-manager repos --enable  ansible-2.9-for-rhel-8-x86_64-rpms
yum install ansible
```
Or you can ansible-navigator

```bash
sudo subscription-manager repos --enable ansible-automation-platform-2.0-early-access-for-rhel-8-x86_64-rpms
# Isntall ansible-navigator
dnf install ansible-navigator
```
On RHEL 9: 

```bash
sudo subscription-manager repos --enable ansible-automation-platform-2.2-for-rhel-9-x86_64-rpms
sudo dnf -y update
# Isntall ansible-navigator
sudo dnf -y install ansible-navigator
```


### 2 - Configure AWS Managed Microsoft AD Active Directory (Optional if you have already AD)

[Configure AWS Managed Microsoft AD Active Directory](https://medium.com/@medkamel555/configure-aws-managed-microsoft-ad-active-directory-and-join-your-linux-ec2-instance-to-the-domain-71fc5a0afaee)

[Directory services options in AWS](https://docs.aws.amazon.com/whitepapers/latest/active-directory-domain-services/directory-services-options-in-aws.html)

- Make sure that your servers are able to reslove the domain name. You can use `dig corp.exmple.com` to check that.

- Make sure that you can reach the domain name via LDAP port (389) and DNS port (53). You need to whitelist your outbound traffic to your AD server.

### 3 - Setup SSH Configuration 
Generate ssh key
```bash
ssh-keygen
```
Copy ssh-key to managed node
```bash
ssh-copy-id user@ip
```
Create config.d directory
```bash
mkdir ~/.ssh/config
```
```bash
Host databaserver-01
   Hostname 10.x.x.x
   User ec2-user
   IdentityFile ~/.ssh/id_rsa

Host webserver-01
   Hostname 10.x.x.x
   User ec2-user
   IdentityFile ~/.ssh/id_rsa
```

### 4 - Inventory configuration

#### 4.1 - Role default config. 


'ad_join' has default varaibles. In order to use this role, you need to ovrride the existent one with your AD credantials.  
You need to provide the details to join linux into domain, like domain user who has right to add client into domain DNS server and user passowrd. The default role varaible is specified:

```yaml
ad_server:
    user: admin # Admin user
    pass: 'test' # Pass user
    domain: corp.exmple.com # domain name
    admin_group: "AWS Delegated Administrators" 
    ou_membership: OU=Computers,DC=example,DC=com
    admin_sudo: 'AWS\ Delegated\ Administrators'
```



#### 4.2 - Inventory structure

The inventory is designed to be more generic for all IT organization. You can create your customized inventory or you ovrride the existent one.


```yaml
[department]
[department:children]
dataseserver
webservers
[dataseserver]
[webservers] 

```

dataserver and webserver group are part of department. Let's assume that you have 2 EC2 instance, one for database server and the other one for webserver. Both, you would like to join them in the AD. So, we can add the AD config in group department.

Under inventory/group_vars/department/department.yml

```yaml
ad_server:
  user: "admin"
  domain: "corp.chhayder.com"
  admin_group: "AWS Delegated Administrators"
  ou_membership: OU=Computers,DC=example,DC=com
  admin_sudo: 'AWS\ Delegated\ Administrators'
```

### 6 - Update vault.yaml

### 7 - Launch the desired playbook

For ansible-playbook: You need to provide the password to decrypt vault.yml
```bash
ansible-playbook  --ask-vault-password  playbooks/ad_join.yaml -i ./inventory
```
For ansible-navigator: 
```bash
 ansible-navigator run -m stdout playbooks/ad_join.yaml --playbook-artifact-enable false  --vault-id one@prompt
 ```

### 7 -Testing :

To verify if your instance is already joined the domain
```bash
sudo realm list
```

```bash
id __user_name__
```
```bash
ssh -l user@corp.exmple.com IPxxxxxxxxxx
```
The user can enter the username in either the username@example.com or EXAMPLE\username format. Then, you are able to connect the server with the specified user.

Futhermore, you can allows your to run programs with the security privileges if your user is part of AWS Delegated Administrators group in the AD


### Leaving the domain

If you want to reverse the process and remove yourself from the domain, simply run the ‘realm leave’ command followed by the domain name, as shown below.

```bash
realm leave example.com
```

