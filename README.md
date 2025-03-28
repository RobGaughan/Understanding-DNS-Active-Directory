# Understanding DNS using Active Directory

> [!NOTE]
> This Lab builds off of the following Labs: 
>  - [Preparing Infrastructure for Active Directory in Azure](https://github.com/RobGaughan/Infrastructure-For-AD-Azure)
>  - [Deploying Active Directory in Azure](https://github.com/RobGaughan/Deploying-Active-Directory-in-Azure/)
>  - [Creating Users, Managing Group Policy, and Accounts](https://github.com/RobGaughan/Creating-Users-Managing-Group-Policy-and-Accounts)
>
> In this lab we will continue to build off the Infrastructure configured in the last three labs listed above

## What is DNS?

DNS Stands for Domain Name System

DNS maps a domain name (example: google.com) to an IP address which can be used by your computer to find a webserver or resources associated with the domain name

## DNS Records
There are over 30 different DNS records for purposes of this lab we will be using the following:  

A-records:
  - Defines the IP address of a host/server
  - Specifically A-records are used for IPv4 Addresses only
  
CNAME-records:
- CNAME stands for Canonical name records
- Using CNAME-records we can create aliases that point to the canonical (true) domain name

## Overview of lab

1. Inspect DNS A-Records
2. Create A-Records on the Server
3. Observe the A-Records we Created
4. Modify the A-record on the server
5. Observe client DNS cache
6. Create "CNAME" records

### DNS caching explanation

First we create an A-Record on Domain-Controller-1 that points `Mainframe.exampledomain.com` to IP address `10.0.0.4`

In our lab we will issue `ping mainframe` on Client-1 when we do that the following will happen:

1. Client-1 checks its cache (no result)
2. Checks host file (no result)
3. Checks DNS server (Record found!)
4. Adds the record to its Local Cache

![dns-1](https://github.com/user-attachments/assets/38d4d17c-3c7d-4505-b695-1ba02cb24307)

Then we will update the A-Record of `Mainframe.exampledomain.com` to point to IP address `8.8.8.8` on Domain-Controller-1

Then on Client-1 we will issue `ping mainframe` this is what happens:
1. Client-1 checks Cache (Record Found! 10.0.0.4) - but it is mismatched!

Client-1 will find the 10.0.0.4 record in its cache even though we have updated the A-Record on the DNS server

Caching is an efficient way to save resources and eventually the cache will time out and update it's record however this will take a while to happen

![dns-2](https://github.com/user-attachments/assets/3560e067-20bf-4cba-9734-0ca454bc3c33)

On Client-1 we can issue `ipconfig /flushdns`  


Using this command we can force Client-1 to update its local DNS cache  
When we do this the updated record with `8.8.8.8` will be updated to Client-1's local DNS cache  
If we attempt to issue `ping mainframe` it will now ping 8.8.8.8

![dns-3](https://github.com/user-attachments/assets/171551bf-db65-49a4-8b6c-46d18a118836)

## Configuration Steps

#### Connect to Client-1 using admin jane_admin

Find client-1 public IP address:  
Virtual Machines > Client-1 > Networking > Network settings 

![image](https://github.com/user-attachments/assets/35965722-d05f-4c90-be6c-a737d71cd161)


login using Remote Desktop Connection 

Make sure to login using domain context:  
Username: EXAMPLEDOMAIN\jane_admin  
Password: LabPassword123

![1](https://github.com/user-attachments/assets/9c63688e-c4d1-4e88-8a89-51f1cc083a79)

Using powershell I attempted to `ping mainframe` however it did not work

When the client tries to resolve a hostname it does the following:
1. Checks Local Cahce (fastest method)
2. Checks Local hostfile (2nd fastest)
3. Checks DNS server

In this case we have not done any configuration yet so the ping fails

![1](https://github.com/user-attachments/assets/1e1638b8-8403-40bd-bf09-e8f549902a66)

To view DNS Cache we can use `ipconfig /displaydns`

Here is the partial output: 
![image](https://github.com/user-attachments/assets/c3f9f100-068b-48f4-b1e4-cd76a25fc4db)

Next we can try `nslookup mainframe` to query the DNS server for mainframe

Since we haven't done any configuration this fails too

![3](https://github.com/user-attachments/assets/93b47213-2818-419c-b320-07395389bbd6)


### Create A-Record on Domain-Controller-1

#### Connect to Domain-Controller-1
Virtual Machines > Domain-Controller-1 > Networking > Network settings 

From here we want to take note of the public IP address we will be using this to remote into Domain-Controller-1

![18](https://github.com/user-attachments/assets/e7e60b6e-0fe5-4f7f-9fb2-396aa86e7c11)

Using Remote Desktop Connection enter the public IP you found in the last step  
Username: LabADMIN  
Password: LabPassword123  
Then click "connect"

![19](https://github.com/user-attachments/assets/05c136e9-4118-496f-a9ca-247459e5c0fb)

#### Create A-Record

Open DNS

![4](https://github.com/user-attachments/assets/868c44b4-0c2d-4d59-87ef-ed42283dfb6c)


Navigate to Domain-Controll > Forward Lookup Zones > exampledomain.com

![5](https://github.com/user-attachments/assets/73ccd023-509f-409c-a30a-10e470344d10)

Right Click > Create New Host (A or AAA)

![6](https://github.com/user-attachments/assets/cea7246b-92f1-41f6-8e58-b02eab53158b)

Create a new record of "mainframe" with IP address: 10.0.0.4

![7](https://github.com/user-attachments/assets/d7853007-f19f-4309-8a0f-bc296fd4fa80)

### Observe the A-Records we Created

Back on Client-1 issue `ping mainframe`

Now we should see a successful ping 
![image](https://github.com/user-attachments/assets/9a88e738-f079-4481-a4f1-09231317dd6f)

Next run `nslookup mainframe`

We should see the following output:  
![image](https://github.com/user-attachments/assets/56abd313-2c57-4f72-a5aa-17a4a6c109a9)


### Change A-record on Domain-Controller-1

Now we will modify the record to point to `8.8.8.8` intstead of `10.0.0.4`

![image](https://github.com/user-attachments/assets/f0e54d8a-157e-4727-8bdf-c0d5cddee0f1)

### Observe client DNS cache

Next issue `ping mainframe` again on Client-1 and observe what address is being used

As we can see Client-1 is still using `10.0.0.4` instead of `8.8.8.8` this is becasue Client-1 Local Cache has not updated 

![image](https://github.com/user-attachments/assets/a0f3087f-fa75-429c-9ab0-e2e70d3e7ddd)

Isusse `ipconfig /displaydns` to view the cache

We can see the mainframe entry is still using `10.0.0.4`

![image](https://github.com/user-attachments/assets/fa991c07-8cda-4e0c-9de6-ae1b12897f1b)

Now we will issue `ipconfig /flushdns` to flush or erase the current cached entries 

![image](https://github.com/user-attachments/assets/db19d522-2bb5-41c2-b29e-6f6633c6f5d8)

Next isusse ipconfig /displaydns to view the cache again

We can now observe that many DNS entries were removed including `mainframe`

![image](https://github.com/user-attachments/assets/bfc9deaa-d955-48ba-a55e-3952667956ec)

Now lets ping mainframe again and then oberve the cache to see if anything changes

Now we can see that `mainframe` updated to `8.8.8.8`

![image](https://github.com/user-attachments/assets/f6de78a1-794a-4efe-bc4e-98539790dde0)

![image](https://github.com/user-attachments/assets/55e85378-9ef7-4975-8ed5-25baf23029d4)

### Create "CNAME" records

Back on Domain-Controller-1 Create a CNAME record that maps `search` to `www.google.com`

![image](https://github.com/user-attachments/assets/3541e585-2236-498e-a516-8bec96915a39)

Now on Client-1 we will issue `ping search`

As you can see it pings www.google.com

![image](https://github.com/user-attachments/assets/00b0ad1e-e90a-431e-a209-a1b775581852)


