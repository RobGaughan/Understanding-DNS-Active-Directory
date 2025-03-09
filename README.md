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
