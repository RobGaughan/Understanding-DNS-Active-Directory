# Understanding DNS using Active Directory

## What is DNS?

DNS Stands for Domain Name System

DNS maps a domain name (example: google.com) to an IP address which can be used by your computer to find a webserver or resources associated with the domain name

## DNS Records
There are over 30 different DNS records for purposes of this lab we will be using the following:  

A-records:
  - Defines the IP address of a host/server
  - Spefically A-records are used for IPv4 Addresses only
  
CNAME-records:
- CNAME stands for Canonical name records
- Using CNAME-records we can create aliases that point to the canonical (true) domain name

## Overview of lab

1. Inspect DNS A-Records
2. Create A-Records on the Server
3. Observe the A-Records we Created
4. Delete records from the server
5. Observe client DNS cache
6. Create "CNAME" records

### DNS caching explaination 

First we create an A-Record on Domain-Controller-1 that points `Mainframe.exampledomain.com` to IP address `10.0.0.4`

In our lab we will issue `ping mainframe` on Client-1 when we do that the following will happen:

1. Client-1 checks its cache (no result)
2. Checks host file (no result)
3. Checks DNS server (Record found!)
4. Adds the record to its Local Cache

![dns-1](https://github.com/user-attachments/assets/38d4d17c-3c7d-4505-b695-1ba02cb24307)

Then we will update the A-Record of `Mainframe.exampledomain.com` to point to IP address `8.8.8.8` on Domain-Controller-1

Then on Client-1 we will isuue `ping mainframe` this is what happens:
1. Client-1 checks Cache (Record Found! 10.0.0.4) - but it is mismatched!

Client-1 will find the 10.0.0.4 record in its cache even though we have updated the A-Record on the DNS server

Caching is an effeicent way to save resources and eventually the cache will time out and update it's record however this will take a while to happen

![dns-2](https://github.com/user-attachments/assets/3560e067-20bf-4cba-9734-0ca454bc3c33)

On Client-1 we can issue `ipconfig /flushdns`  


Using this command we can force Client-1 to update its local DNS cache  
When we do this the updated record with `8.8.8.8` will be updated to Client-1's local DNS cache  
If we attempt to issue `ping mainframe` it will now ping 8.8.8.8

![dns-3](https://github.com/user-attachments/assets/171551bf-db65-49a4-8b6c-46d18a118836)





