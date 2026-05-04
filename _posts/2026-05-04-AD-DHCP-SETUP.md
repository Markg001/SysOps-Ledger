---
title: " DHCP Server Setup on Windows Server 2022"
date: 2026-05-04
categories: [system administration]
tags: [active directory, dhcp, windows server, ad ds, setup]
---
## DHCP Server Setup
In the previous post, we have setup Active Directory Domain Services and DNS server on Windows Server 2022. Now, we will setup DHCP server on the same server. DHCP (Dynamic Host Configuration Protocol) is a network protocol that allows network administrators to automate the process of configuring devices on IP networks. It allows devices to receive IP addresses and other network configuration information automatically from a DHCP server.
1. Open Server Manager and then click on Add roles and features to open the Add Roles and Features Wizard.
<img src="/assets/img/posts/dhcp-setup/add-roles.png" alt="Add Roles and Features" class="shadow">
2. Click Next on the Before you begin page.
3. Select Role-based or feature-based installation and click Next.
4. Select the server you want to install the DHCP role on and click Next.
5. On the Select server roles page, check the box next to DHCP Server and click Next.
6. Click Next on the Select features page.
<img src="/assets/img/posts/dhcp-setup/dhcp-role.png" alt="Select DHCP Role" class="shadow">
7. On the DHCP Server page, click Next.
8. On the Confirm installation selections page, review your selections and click Install.
After the installation is complete, click on the Complete DHCP configuration link to open the DHCP Post-Install configuration wizard.
9. On the DHCP Post-Install configuration wizard, click Next.
<img src="/assets/img/posts/dhcp-setup/post-install.png" alt="DHCP Post-Install Configuration" class="shadow">
10. On the Authorize DHCP Server page, select the option to Authorize this DHCP server in Active Directory and click Next.
11. On the Summary page, review your selections and click Commit.
12. After the configuration is complete, click Close to exit the wizard.
Now, the DHCP server is installed and authorized in Active Directory. You can now create DHCP scopes and configure DHCP options to start assigning IP addresses to clients on your network.
### Creating a DHCP Scope
A DHCP scope is a range of IP addresses that the DHCP server can assign to clients. To create a DHCP scope, follow these steps:
1. Open the DHCP management console by clicking on Tools in Server Manager and selecting DHCP.
2. In the DHCP management console, expand the server name and then right-click on IPv4 and select New Scope.
<img src="/assets/img/posts/dhcp-setup/new-scope.png" alt="New DHCP Scope" class="shadow">
3. On the New Scope Wizard, click Next.
4. On the Scope Name page, enter a name and description for the scope and click Next.
5. On the IP Address Range page, enter the starting and ending IP addresses for the scope, and the subnet mask. Click Next.
<img src="/assets/img/posts/dhcp-setup/ip-range.png" alt="DHCP Scope IP Range" class="shadow">
6. On the Add Exclusions and Delay page, you can add any IP addresses that you want to exclude from the scope. Click Next.
<img src="/assets/img/posts/dhcp-setup/exclusions.png" alt="DHCP Scope Exclusions" class="shadow">
7. On the Lease Duration page, specify the lease duration for the IP addresses assigned by this scope and click Next.
8. On the Configure DHCP Options page, select Yes, I want to configure these options now and click Next.
<img src="/assets/img/posts/dhcp-setup/configure-options.png" alt="Configure DHCP Options" class="shadow">
9. On the Router (Default Gateway) page, enter the IP address of the default gateway for the clients and click Next.
<img src="/assets/img/posts/dhcp-setup/router.png" alt="DHCP Scope Router" class="shadow">
10. On the Domain Name and DNS Servers page, enter the parent domain name and the IP address of the DNS server (which is the same server in this case) and click Next.
<img src="/assets/img/posts/dhcp-setup/dns.png" alt="DHCP Scope DNS" class="shadow">
11. On the WINS Servers page, you can enter the IP address of any WINS servers if you have them. leave the fields empty if you don't have any. Click Next.
12. On the Activate Scope page, select Yes, I want to activate this scope now and click Next.
<img src="/assets/img/posts/dhcp-setup/activate-scope.png" alt="Activate DHCP Scope" class="shadow">

## Advanced DHCP configuration
In addition to creating a DHCP scope, you can also configure advanced DHCP options such as DHCP reservations and DHCP policies. DHCP reservations allow you to reserve a specific IP address for a specific device based on its MAC address. DHCP policies allow you to apply specific settings to clients based on criteria such as vendor class or user class. 
1. Configure “Client1” to get IP configuration automatically from DHCP Server
<img src="/assets/img/posts/dhcp-setup/client1.png" alt="Client1 DHCP Configuration" class="shadow">
2. Add a reservation for “Client1”
Open DHCP console, expand the scope, expand “Address Leases”, r-click on the lease and choose add to reservation
<img src="/assets/img/posts/dhcp-setup/reservation.png" alt="DHCP Reservation" class="shadow">
3. Configure a reservation option for “Client1” to assign the IP address 8.8.8.8 as a DNS server IP for “Client1”
This reservation will override Server Options and Scope Options
Go to reservation, r-click on the reservation for “Client1”, select “Configure Options”, check the box next to 006 DNS Servers, click on the value field and enter 8.8.8.8 or any other DNS server IP address you want to assign to “Client1”
<img src="/assets/img/posts/dhcp-setup/reservation-options.png" alt="DHCP Reservation Options" class="shadow">
<img src="/assets/img/posts/dhcp-setup/reservation-options2.png" alt="DHCP Reservation Options 2" class="shadow">
4. Create a User Class named “Admins” on the DHCP server, and configure a policy for this user class to assign a
default gateway of “10.10.10.250”.
a. R-click on IPv4, select “Define User Classes…”
<img src="/assets/img/posts/dhcp-setup/user-class.png" alt="Define DHCP User Class" class="shadow">
b. Click on Add, enter “Admins”  and the Value “Admins”, the value
must be entered in the ASCI field as shown in the figure below, then click OK
<img src="/assets/img/posts/dhcp-setup/add-user-class.png" alt="Add DHCP User Class" class="shadow">
<img src="/assets/img/posts/dhcp-setup/admins.png" alt="New DHCP Policy" class="shadow" >
c. Now, we create the policy for this user class.
Expand IPv4, r-click on “Policy”, select “New Policy”
<img src="/assets/img/posts/dhcp-setup/new-policy.png" alt="New DHCP Policy" class="shadow">
d.Type in the policy name “Admins” and click Next.
e. Click on “Add” to add the condition.
f. For the criteria, select “User Class”, the operator “Equal”, the Value “Admins, then click “Add” and
“OK”
<img src="/assets/img/posts/dhcp-setup/policy-condition.png" alt="DHCP Policy Condition" class="shadow">
g. In the “conditions” windows click Next.
h. In the “Configure Setting for the Policy” windows, check on option “003 Router” and type in the IP
address “10.10.10.250”, then click “Add”, and “Next”.
i. In the Summary windows, click finish.
j. Assign this user class to the network interface on Client1.
Go to the click machine “Client1” and open CMD as administrator, and type the following command.
Make sure you replace “Local Area Connection” with the interface name on your client machine
```bashnetsh interface ipv4 set interface "Local Area Connection" admin=enabled userclass=Admins
```
k. Now, we will release and renew the IP address on Client1 to apply the new policy.
```bashipconfig /release
ipconfig /renew
```
l. After renewing the IP address, you can check the new IP configuration on Client1 by running the following command:
```bashipconfig /all
``` 
You should see that the default gateway is now set to “10.10.10.250”, which is the value we configured in the policy for the “Admins” user class. This means that the policy is working correctly and is applying the specified settings to clients that belong to the “Admins” user class.
## Conclusion
In this post, we have successfully setup a DHCP server on Windows Server 2022 and created a DHCP scope to assign IP addresses to clients on the network. We also explored advanced DHCP configuration options such as DHCP reservations and DHCP policies, which allow us to customize the DHCP settings for specific clients or groups of clients. With DHCP, we can automate the process of assigning IP addresses and other network configuration information to devices on our network, making it easier to manage and maintain our network infrastructure.
