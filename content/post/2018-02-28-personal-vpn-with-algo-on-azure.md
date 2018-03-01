---
title: "Personal Vpn with Algo on Azure"
date: 2018-02-28T11:51:41+01:00
subtitle: "Deploy a personal VPN. Because VPN providers are expensive crap."
tags: ["vpn", "azure", "python", "github"]
categories: ["networking", "cloud"]
draft: true
---

Let's create a VPN server on Azure (other cloud providers are supported).

<!--more-->

# Algo
[Algo](https://github.com/trailofbits/algo) github repository.
[Algo](https://blog.trailofbits.com/2016/12/12/meet-algo-the-vpn-that-works/) official tutorial.

Clone the repository
```shell
git clone https://github.com/trailofbits/algo.git
cd algo
```

Install dependencies
```shell
sudo apt-get update && sudo apt-get install \
    build-essential \
    libssl-dev \
    libffi-dev \
    python-dev \
    python-pip \
    python-setuptools \
    python-virtualenv -y
```

The remaining python dependencies
```shell
python -m virtualenv env && source env/bin/activate && python -m pip install -U pip && python -m pip install -r requirements.txt
```

*Note: the installation took about 100Mb*

Open *config.cfg* with a text editor and add as many users as you need.

Run algo and follow the instructions on screen.
```shell
./algo
```

# Azure setup
(Official guide)[https://github.com/trailofbits/algo/blob/master/docs/cloud-azure.md]

From the [Azure Portal]( https://portal.azure.com/) go to **Azure Active Directory**.
Select **App registrations** and click **New Application Registration**.
Fill the form
- Name: algo-personal-vpn *(it can be any name)*
- Application type: Web app / API
- Sign-on URL: https://www.teosoft.it *(it can be any URL)*

Copy and save somewhere the *Application ID*. Click on **Settings**, **Keys**, add a *Key description* like *algo* (it will be the name of the virtual machine) with *Never expires*. Click save and the copy and save somewhere the *Value* (this is the *Secret ID*).

Go to the Main menu, **Azure Active Directory** and click on Properties. Copy and save somewhere the *Directory ID* (in the script it will be referred as *tenant*).

Go to the Main menu, **Subscriptions** and click on the subscription you want you use in Algo. Copy and save the *Subscription ID*. Inside the **Subscription** go to **Acces Control** add a new item, select "Contributor". Search for *algo-personal-vpn*, add it as a member and **Save**.

Now insert the values inside the script to continue with the wizard.

Optionally create the credentials file ```~/.azure/credentials```:
```
[default]
client_id=
secret=
tenant=
subscription_id=
```

## Wizard
The wizard will ask some question and procede to create a *Basic A0 (1 vcpu, 0.75 GB memory)* with the name *algo*. The pricing is actually 0,015 €/hour or 11,08 €/month.

Here is the result of a test run
```
What provider would you like to use?
  1. DigitalOcean
  2. Amazon EC2
  3. Microsoft Azure
  4. Google Compute Engine
  5. Install to existing Ubuntu 16.04 server

Enter the number of your desired provider
: 3

Enter your azure secret id (https://github.com/trailofbits/algo/blob/master/docs/cloud-azure.md)
You can skip this step if you want to use your defaults credentials from ~/.azure/credentials
[pasted values will not be displayed]
[...]:

Enter your azure tenant id (https://github.com/trailofbits/algo/blob/master/docs/cloud-azure.md)
You can skip this step if you want to use your defaults credentials from ~/.azure/credentials
[pasted values will not be displayed]
[...]:

Enter your azure client id (application id) (https://github.com/trailofbits/algo/blob/master/docs/cloud-azure.md)
You can skip this step if you want to use your defaults credentials from ~/.azure/credentials
[pasted values will not be displayed]
[...]:

Enter your azure subscription id (https://github.com/trailofbits/algo/blob/master/docs/cloud-azure.md)
You can skip this step if you want to use your defaults credentials from ~/.azure/credentials
[pasted values will not be displayed]
[...]:

Name the vpn server:
[algo]:


What region should the server be located in? (https://azure.microsoft.com/en-us/regions/)
  1.  South Central US
  2.  Central US
  3.  North Europe
  4.  West Europe
  5.  Southeast Asia
  6.  Japan West
  7.  Japan East
  8.  Australia Southeast
  9.  Australia East
  10. Canada Central
  11. West US 2
  12. West Central US
  13. UK South
  14. UK West
  15. West US
  16. Brazil South
  17. Canada East
  18. Central India
  19. East Asia
  20. Germany Central
  21. Germany Northeast
  22. Korea Central
  23. Korea South
  24. North Central US
  25. South India
  26. West India
  27. East US
  28. East US 2

Enter the number of your desired region:
[1]: 4

Do you want macOS/iOS clients to enable "VPN On Demand" when connected to cellular networks?
[y/N]:

Do you want macOS/iOS clients to enable "VPN On Demand" when connected to Wi-Fi?
[y/N]:

Do you want to install a DNS resolver on this VPN server, to block ads while surfing?
[y/N]:

Do you want each user to have their own account for SSH tunneling?
[y/N]:

Do you want to apply operating system security enhancements on the server? (warning: replaces your sshd_config)
[y/N]:

Do you want the VPN to support Windows 10 or Linux Desktop clients? (enables compatible ciphers and key exchange, less secure)
[y/N]:

Do you want to retain the CA key? (required to add users in the future, but less secure)
[y/N]:

PLAY [Configure the server] ****************************************************

TASK [setup] *******************************************************************
ok: [localhost]

TASK [Generate the SSH private key] ********************************************
changed: [localhost]

TASK [Generate the SSH public key] *********************************************
ok: [localhost]

TASK [Change mode for the SSH private key] *************************************
ok: [localhost]

TASK [Ensure the dynamic inventory exists] *************************************
changed: [localhost]

TASK [cloud-azure : set_fact] **************************************************
ok: [localhost]

TASK [cloud-azure : Create a resource group] ***********************************
changed: [localhost]

TASK [cloud-azure : Create a virtual network] **********************************
changed: [localhost]

TASK [cloud-azure : Create a security group] ***********************************
changed: [localhost]

TASK [cloud-azure : Create a subnet] *******************************************
changed: [localhost]

TASK [cloud-azure : Create an instance] ****************************************
changed: [localhost]

TASK [cloud-azure : set_fact] **************************************************
ok: [localhost]

TASK [cloud-azure : Ensure the network interface includes all required parameters] ***
changed: [localhost]

TASK [cloud-azure : Add the instance to an inventory group] ********************
changed: [localhost]

TASK [cloud-azure : set_fact] **************************************************
ok: [localhost]

TASK [cloud-azure : Ensure the group azure exists in the dynamic inventory file] ***
changed: [localhost]

TASK [cloud-azure : Populate the dynamic inventory] ****************************
changed: [localhost]

TASK [Wait until SSH becomes ready...] *****************************************
ok: [localhost]

TASK [A short pause, in order to be sure the instance is ready] ****************
Pausing for 20 seconds
(ctrl+C then 'C' = continue early, ctrl+C then 'A' = abort)
ok: [localhost]

TASK [Ensure the local ssh directory is exist] *********************************
ok: [localhost]

TASK [Copy the algo ssh key to the local ssh directory] ************************
changed: [localhost]

PLAY [Configure the server and install required software] **********************

TASK [Check the system] ********************************************************
changed: [13.80.20.110]

TASK [Ubuntu | Install prerequisites] ******************************************
changed: [13.80.20.110]

TASK [Ubuntu | Configure defaults] *********************************************
changed: [13.80.20.110]

TASK [FreeBSD / HardenedBSD | Install prerequisites] ***************************
skipping: [13.80.20.110]

TASK [FreeBSD / HardenedBSD | Configure defaults] ******************************
skipping: [13.80.20.110]

TASK [set_fact] ****************************************************************
skipping: [13.80.20.110]

TASK [Gather Facts] ************************************************************
ok: [13.80.20.110]

TASK [Ensure the algo ssh key exist on the server] *****************************
changed: [13.80.20.110]

TASK [Enable IPv6] *************************************************************
skipping: [13.80.20.110]

TASK [Set facts if the deployment in a cloud] **********************************
ok: [13.80.20.110]

TASK [Generate password for the CA key] ****************************************
changed: [13.80.20.110 -> localhost]

TASK [Generate p12 export password] ********************************************
changed: [13.80.20.110 -> localhost]

TASK [Define password facts] ***************************************************
ok: [13.80.20.110]

TASK [Define the commonName] ***************************************************
ok: [13.80.20.110]

TASK [common : Install software updates] ***************************************
changed: [13.80.20.110]

TASK [common : Check if reboot is required] ************************************
changed: [13.80.20.110]

TASK [common : Reboot] *********************************************************
skipping: [13.80.20.110]

TASK [common : Wait until SSH becomes ready...] ********************************
skipping: [13.80.20.110]

TASK [common : Disable MOTD on login and SSHD] *********************************
changed: [13.80.20.110] => (item={u'regexp': u'^session.*optional.*pam_motd.so.*', u'line': u'# MOTD DISABLED', u'file': u'/etc/pam.d/login'})
changed: [13.80.20.110] => (item={u'regexp': u'^session.*optional.*pam_motd.so.*', u'line': u'# MOTD DISABLED', u'file': u'/etc/pam.d/sshd'})

TASK [common : Install system specific tools] **********************************
ok: [13.80.20.110] => (item=[u'ifupdown'])

TASK [common : Ensure the interfaces directory exists] *************************
ok: [13.80.20.110]

TASK [common : Loopback for services configured] *******************************
changed: [13.80.20.110]

TASK [common : Loopback included into the network config] **********************
changed: [13.80.20.110]

RUNNING HANDLER [common : restart loopback] ************************************
changed: [13.80.20.110]

TASK [common : set_fact] *******************************************************
ok: [13.80.20.110]

TASK [common : set_fact] *******************************************************
skipping: [13.80.20.110]

TASK [common : Loopback included into the rc config] ***************************
skipping: [13.80.20.110]

TASK [common : Enable the gateway features] ************************************
skipping: [13.80.20.110] => (item={u'value': u'"YES"', u'param': u'firewall_enable'})
skipping: [13.80.20.110] => (item={u'value': u'"open"', u'param': u'firewall_type'})
skipping: [13.80.20.110] => (item={u'value': u'"YES"', u'param': u'gateway_enable'})
skipping: [13.80.20.110] => (item={u'value': u'"YES"', u'param': u'natd_enable'})
skipping: [13.80.20.110] => (item={u'value': u'""', u'param': u'natd_interface'})
skipping: [13.80.20.110] => (item={u'value': u'"-dynamic -m"', u'param': u'natd_flags'})

TASK [common : Install tools] **************************************************
changed: [13.80.20.110] => (item=[u'git', u'screen', u'apparmor-utils', u'uuid-runtime', u'coreutils', u'iptables-persistent', u'cgroup-tools', u'openssl'])

TASK [common : Sysctl tuning] **************************************************
changed: [13.80.20.110] => (item={u'item': u'net.ipv4.ip_forward', u'value': 1})
changed: [13.80.20.110] => (item={u'item': u'net.ipv4.conf.all.forwarding', u'value': 1})
changed: [13.80.20.110] => (item={u'item': u'net.ipv6.conf.all.forwarding', u'value': 1})

TASK [vpn : Ensure that the strongswan group exist] ****************************
changed: [13.80.20.110]

TASK [vpn : Ensure that the strongswan user exist] *****************************
changed: [13.80.20.110]

TASK [vpn : set_fact] **********************************************************
ok: [13.80.20.110]

TASK [vpn : Ubuntu | Install strongSwan] ***************************************
changed: [13.80.20.110]

TASK [vpn : Ubuntu | Enforcing ipsec with apparmor] ****************************
skipping: [13.80.20.110] => (item=/usr/lib/ipsec/charon)
skipping: [13.80.20.110] => (item=/usr/lib/ipsec/lookip)
skipping: [13.80.20.110] => (item=/usr/lib/ipsec/stroke)

TASK [vpn : Ubuntu | Enable services] ******************************************
ok: [13.80.20.110] => (item=apparmor)
ok: [13.80.20.110] => (item=strongswan)
ok: [13.80.20.110] => (item=netfilter-persistent)

TASK [vpn : Ubuntu | Ensure that the strongswan service directory exist] *******
changed: [13.80.20.110]

TASK [vpn : Ubuntu | Setup the cgroup limitations for the ipsec daemon] ********
changed: [13.80.20.110]

TASK [vpn : Iptables configured] ***********************************************
changed: [13.80.20.110] => (item={u'dest': u'/etc/iptables/rules.v4', u'src': u'rules.v4.j2'})

TASK [vpn : Iptables configured] ***********************************************
skipping: [13.80.20.110] => (item={u'dest': u'/etc/iptables/rules.v6', u'src': u'rules.v6.j2'})

TASK [vpn : FreeBSD / HardenedBSD | Get the existing kernel parameters] ********
skipping: [13.80.20.110]

TASK [vpn : FreeBSD / HardenedBSD | Set the rebuild_needed fact] ***************
skipping: [13.80.20.110] => (item=IPSEC)
skipping: [13.80.20.110] => (item=IPSEC_NAT_T)
skipping: [13.80.20.110] => (item=crypto)

TASK [vpn : FreeBSD / HardenedBSD | Make the kernel config] ********************
skipping: [13.80.20.110]

TASK [vpn : FreeBSD / HardenedBSD | Ensure the all options are enabled] ********
skipping: [13.80.20.110] => (item=options	IPSEC)
skipping: [13.80.20.110] => (item=options IPSEC_NAT_T)
skipping: [13.80.20.110] => (item=device	crypto)

TASK [vpn : HardenedBSD | Determine the sources] *******************************
skipping: [13.80.20.110]

TASK [vpn : FreeBSD | Determine the sources] ***********************************
skipping: [13.80.20.110]

TASK [vpn : FreeBSD / HardenedBSD | Increase the git postBuffer size] **********
skipping: [13.80.20.110]

TASK [vpn : FreeBSD / HardenedBSD | Fetching the sources...] *******************
skipping: [13.80.20.110]

TASK [vpn : FreeBSD / HardenedBSD | Fetching the sources...] *******************
skipping: [13.80.20.110]

TASK [vpn : FreeBSD / HardenedBSD | The kernel is being built...] **************
skipping: [13.80.20.110]

TASK [vpn : FreeBSD / HardenedBSD | The kernel is being built...] **************
skipping: [13.80.20.110]

TASK [vpn : FreeBSD / HardenedBSD | Reboot] ************************************
skipping: [13.80.20.110]

TASK [vpn : FreeBSD / HardenedBSD | Enable strongswan] *************************
skipping: [13.80.20.110]

TASK [vpn : Install strongSwan] ************************************************
ok: [13.80.20.110]

TASK [vpn : Setup the config files from our templates] *************************
changed: [13.80.20.110] => (item={u'dest': u'/etc/strongswan.conf', u'src': u'strongswan.conf.j2', u'group': u'root', u'mode': u'0644', u'owner': u'root'})
changed: [13.80.20.110] => (item={u'dest': u'/etc/ipsec.conf', u'src': u'ipsec.conf.j2', u'group': u'root', u'mode': u'0644', u'owner': u'root'})
changed: [13.80.20.110] => (item={u'dest': u'/etc/ipsec.secrets', u'src': u'ipsec.secrets.j2', u'group': u'root', u'mode': u'0600', u'owner': u'strongswan'})

TASK [vpn : Get loaded plugins] ************************************************
changed: [13.80.20.110]

TASK [vpn : Disable unneeded plugins] ******************************************
changed: [13.80.20.110] => (item=attr)
skipping: [13.80.20.110] => (item=sha2)
skipping: [13.80.20.110] => (item=revocation)
skipping: [13.80.20.110] => (item=kernel-netlink)
changed: [13.80.20.110] => (item=test-vectors)
changed: [13.80.20.110] => (item=md4)
changed: [13.80.20.110] => (item=rc2)
skipping: [13.80.20.110] => (item=pkcs12)
changed: [13.80.20.110] => (item=pkcs1)
skipping: [13.80.20.110] => (item=gcm)
changed: [13.80.20.110] => (item=xcbc)
skipping: [13.80.20.110] => (item=pgp)
skipping: [13.80.20.110] => (item=hmac)
skipping: [13.80.20.110] => (item=pkcs8)
changed: [13.80.20.110] => (item=agent)
changed: [13.80.20.110] => (item=sshkey)
skipping: [13.80.20.110] => (item=stroke)
skipping: [13.80.20.110] => (item=random)
skipping: [13.80.20.110] => (item=pubkey)
skipping: [13.80.20.110] => (item=aes)
skipping: [13.80.20.110] => (item=pkcs7)
changed: [13.80.20.110] => (item=resolve)
changed: [13.80.20.110] => (item=connmark)
changed: [13.80.20.110] => (item=constraints)
changed: [13.80.20.110] => (item=gmp)
changed: [13.80.20.110] => (item=md5)
changed: [13.80.20.110] => (item=fips-prf)
skipping: [13.80.20.110] => (item=pem)
changed: [13.80.20.110] => (item=sha1)
skipping: [13.80.20.110] => (item=nonce)
changed: [13.80.20.110] => (item=updown)
skipping: [13.80.20.110] => (item=openssl)
skipping: [13.80.20.110] => (item=x509)
changed: [13.80.20.110] => (item=dnskey)
skipping: [13.80.20.110] => (item=socket-default)

TASK [vpn : Ensure that required plugins are enabled] **************************
skipping: [13.80.20.110] => (item=attr)
changed: [13.80.20.110] => (item=sha2)
changed: [13.80.20.110] => (item=revocation)
changed: [13.80.20.110] => (item=kernel-netlink)
skipping: [13.80.20.110] => (item=test-vectors)
skipping: [13.80.20.110] => (item=md4)
skipping: [13.80.20.110] => (item=rc2)
changed: [13.80.20.110] => (item=pkcs12)
skipping: [13.80.20.110] => (item=pkcs1)
changed: [13.80.20.110] => (item=gcm)
skipping: [13.80.20.110] => (item=xcbc)
changed: [13.80.20.110] => (item=pgp)
changed: [13.80.20.110] => (item=hmac)
skipping: [13.80.20.110] => (item=agent)
changed: [13.80.20.110] => (item=pkcs8)
skipping: [13.80.20.110] => (item=sshkey)
changed: [13.80.20.110] => (item=stroke)
changed: [13.80.20.110] => (item=random)
changed: [13.80.20.110] => (item=pubkey)
changed: [13.80.20.110] => (item=aes)
changed: [13.80.20.110] => (item=pkcs7)
skipping: [13.80.20.110] => (item=resolve)
skipping: [13.80.20.110] => (item=connmark)
skipping: [13.80.20.110] => (item=constraints)
skipping: [13.80.20.110] => (item=gmp)
skipping: [13.80.20.110] => (item=md5)
skipping: [13.80.20.110] => (item=fips-prf)
changed: [13.80.20.110] => (item=pem)
skipping: [13.80.20.110] => (item=sha1)
changed: [13.80.20.110] => (item=nonce)
skipping: [13.80.20.110] => (item=updown)
changed: [13.80.20.110] => (item=openssl)
changed: [13.80.20.110] => (item=x509)
skipping: [13.80.20.110] => (item=dnskey)
changed: [13.80.20.110] => (item=socket-default)

TASK [vpn : Ensure the pki directory does not exist] ***************************
skipping: [13.80.20.110]

TASK [vpn : Ensure the pki directories exist] **********************************
changed: [13.80.20.110 -> localhost] => (item=ecparams)
changed: [13.80.20.110 -> localhost] => (item=certs)
changed: [13.80.20.110 -> localhost] => (item=crl)
changed: [13.80.20.110 -> localhost] => (item=newcerts)
changed: [13.80.20.110 -> localhost] => (item=private)
changed: [13.80.20.110 -> localhost] => (item=reqs)

TASK [vpn : Ensure the files exist] ********************************************
changed: [13.80.20.110 -> localhost] => (item=.rnd)
changed: [13.80.20.110 -> localhost] => (item=private/.rnd)
changed: [13.80.20.110 -> localhost] => (item=index.txt)
changed: [13.80.20.110 -> localhost] => (item=index.txt.attr)
changed: [13.80.20.110 -> localhost] => (item=serial)

TASK [vpn : Generate the openssl server configs] *******************************
changed: [13.80.20.110 -> localhost]

TASK [vpn : Build the CA pair] *************************************************
changed: [13.80.20.110 -> localhost]

TASK [vpn : Copy the CA certificate] *******************************************
changed: [13.80.20.110 -> localhost]

TASK [vpn : Generate the serial number] ****************************************
changed: [13.80.20.110 -> localhost]

TASK [vpn : Build the server pair] *********************************************
changed: [13.80.20.110 -> localhost]

TASK [vpn : Build the client's pair] *******************************************
changed: [13.80.20.110 -> localhost] => (item=gabriele)
changed: [13.80.20.110 -> localhost] => (item=davide)
changed: [13.80.20.110 -> localhost] => (item=domenico)
changed: [13.80.20.110 -> localhost] => (item=stefano)

TASK [vpn : Build the client's p12] ********************************************
changed: [13.80.20.110 -> localhost] => (item=gabriele)
changed: [13.80.20.110 -> localhost] => (item=davide)
changed: [13.80.20.110 -> localhost] => (item=domenico)
changed: [13.80.20.110 -> localhost] => (item=stefano)

TASK [vpn : Copy the p12 certificates] *****************************************
changed: [13.80.20.110 -> localhost] => (item=gabriele)
changed: [13.80.20.110 -> localhost] => (item=davide)
changed: [13.80.20.110 -> localhost] => (item=domenico)
changed: [13.80.20.110 -> localhost] => (item=stefano)

TASK [vpn : Get active users] **************************************************
changed: [13.80.20.110 -> localhost]

TASK [vpn : Revoke non-existing users] *****************************************
skipping: [13.80.20.110] => (item=gabriele)
skipping: [13.80.20.110] => (item=davide)
skipping: [13.80.20.110] => (item=domenico)
skipping: [13.80.20.110] => (item=stefano)

TASK [vpn : Genereate new CRL file] ********************************************
skipping: [13.80.20.110]

TASK [vpn : Copy the CRL to the vpn server] ************************************
skipping: [13.80.20.110]

TASK [vpn : Copy the keys to the strongswan directory] *************************
changed: [13.80.20.110] => (item={u'dest': u'/etc/ipsec.d/cacerts/ca.crt', u'src': u'configs/13.80.20.110/pki/cacert.pem', u'group': u'root', u'mode': u'0600', u'owner': u'strongswan'})
changed: [13.80.20.110] => (item={u'dest': u'/etc/ipsec.d/certs/13.80.20.110.crt', u'src': u'configs/13.80.20.110/pki/certs/13.80.20.110.crt', u'group': u'root', u'mode': u'0600', u'owner': u'strongswan'})
changed: [13.80.20.110] => (item={u'dest': u'/etc/ipsec.d/private/13.80.20.110.key', u'src': u'configs/13.80.20.110/pki/private/13.80.20.110.key', u'group': u'root', u'mode': u'0600', u'owner': u'strongswan'})

TASK [vpn : Register p12 PayloadContent] ***************************************
changed: [13.80.20.110 -> localhost] => (item=gabriele)
changed: [13.80.20.110 -> localhost] => (item=davide)
changed: [13.80.20.110 -> localhost] => (item=domenico)
changed: [13.80.20.110 -> localhost] => (item=stefano)

TASK [vpn : Set facts for mobileconfigs] ***************************************
ok: [13.80.20.110 -> localhost]

TASK [vpn : Build the mobileconfigs] *******************************************
changed: [13.80.20.110 -> localhost] => (item=(censored due to no_log))
changed: [13.80.20.110 -> localhost] => (item=(censored due to no_log))
changed: [13.80.20.110 -> localhost] => (item=(censored due to no_log))
changed: [13.80.20.110 -> localhost] => (item=(censored due to no_log))

TASK [vpn : Build the strongswan app android config] ***************************
changed: [13.80.20.110 -> localhost] => (item=(censored due to no_log))
changed: [13.80.20.110 -> localhost] => (item=(censored due to no_log))
changed: [13.80.20.110 -> localhost] => (item=(censored due to no_log))
changed: [13.80.20.110 -> localhost] => (item=(censored due to no_log))

TASK [vpn : Build the android helper html] *************************************
changed: [13.80.20.110 -> localhost] => (item=(censored due to no_log))
changed: [13.80.20.110 -> localhost] => (item=(censored due to no_log))
changed: [13.80.20.110 -> localhost] => (item=(censored due to no_log))
changed: [13.80.20.110 -> localhost] => (item=(censored due to no_log))

TASK [vpn : Build the client ipsec config file] ********************************
changed: [13.80.20.110 -> localhost] => (item=gabriele)
changed: [13.80.20.110 -> localhost] => (item=davide)
changed: [13.80.20.110 -> localhost] => (item=domenico)
changed: [13.80.20.110 -> localhost] => (item=stefano)

TASK [vpn : Build the client ipsec secret file] ********************************
changed: [13.80.20.110 -> localhost] => (item=gabriele)
changed: [13.80.20.110 -> localhost] => (item=davide)
changed: [13.80.20.110 -> localhost] => (item=domenico)
changed: [13.80.20.110 -> localhost] => (item=stefano)

TASK [vpn : Create the windows check file] *************************************
skipping: [13.80.20.110]

TASK [vpn : Check if the windows check file exists] ****************************
ok: [13.80.20.110 -> localhost]

TASK [vpn : Build the windows client powershell script] ************************
skipping: [13.80.20.110] => (item=gabriele)
skipping: [13.80.20.110] => (item=davide)
skipping: [13.80.20.110] => (item=domenico)
skipping: [13.80.20.110] => (item=stefano)

TASK [vpn : Restrict permissions for the local private directories] ************
changed: [13.80.20.110 -> localhost] => (item=configs/13.80.20.110)

RUNNING HANDLER [vpn : restart strongswan] *************************************
changed: [13.80.20.110]

RUNNING HANDLER [vpn : daemon-reload] ******************************************
changed: [13.80.20.110]

RUNNING HANDLER [vpn : restart iptables] ***************************************
changed: [13.80.20.110]

TASK [vpn : strongSwan started] ************************************************
ok: [13.80.20.110]

TASK [debug] *******************************************************************
ok: [13.80.20.110] => {
  "msg": [
      [
          "\"#                          Congratulations!                            #\"",
          "\"#                     Your Algo server is running.                     #\"",
          "\"#    Config files and certificates are in the ./configs/ directory.    #\"",
          "\"#              Go to https://whoer.net/ after connecting               #\"",
          "\"#        and ensure that all your traffic passes through the VPN.      #\"",
          "\"#               Local DNS resolver 172.16.0.1              #\"",
          ""
      ],
      "    \"#                The p12 and SSH keys password for new users is *redacted*             #\"\n",
      "    ",
      "    \"#      Shell access: ssh -i configs/algo.pem ubuntu@13.80.20.110        #\"\n"
  ]
}

TASK [Delete the CA key] *******************************************************
changed: [13.80.20.110 -> localhost]

PLAY RECAP *********************************************************************
13.80.20.110               : ok=61   changed=47   unreachable=0    failed=0
localhost                  : ok=21   changed=12   unreachable=0    failed=0
```

## Client configuration
### Debian with xfce
Install the default strongswan plugin (the offical instrusctions says that there is a problem but)

```shell
sudo apt install network-manager-strongswan
```

Right click on the network manager icon, disable and then enable the networking.
Click on the netowrk manager, select *VPN connections* -> *Add a VPN connection*. Choose the strongswan plugin.
Fill in the options
- Name: vpn-algo (or anything else)
- Certificate: browse to ~/algo/configs/ipoftheserver/cacert.pem
- Client
  - Authentication: Certificate/Private key
  - Certificate: ~/algo/configs/ipoftheserver/pki/certs/user-name.crt
  - Private key: ~/algo/configs/ipoftheserver/pki/private/user-name.key
- Options
  - Check Request an inner IP address
  - Optionally check Enforce UDP encapsulation
  - Optionally check Use IP compression
