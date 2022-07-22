# 802.1x Configuration with Nornir

The script is intended to automatically configure all commands in global configuration mode for enabling 802.1x Authentication.
After that all necessary settings on interface level will be done on a per interface basis.
The dection of which interface are present, an which are acutally access ports, is done by the script.
Only access ports are configured.
All this will done in parallel on multiple devices.

Illustrate the following concepts:
- Set all necessary global Settings for 802.1x on IOS-based Device
- Setting all neccessary interface configurations based on check if description and interface type
- Add the Device to ISE with all secrets and needed groups

![diagram](./802.1x-diagram.png)

## Installation
Please use NORNIR Version 3.0
Following Packtes, Modules and Requirements are needed:
    
    nornir==3.1.0
    nornir-napalm==0.1.2
    nornir-netmiko==0.1.1
    paramiko==2.7.2
   
For more informations see ---> https://github.com/nornir-automation/nornir
Python Version must be at least v3.6.8

## Usage
Please create the neccessary inventory files that nornir works with, and adjust them to your enviroment.
For that please use the following files:

* nornir3_config.yaml     ---> basis config of how nornir should handle connections and where to gather informations
* nornir3_defaults.yaml   ---> can be left empty, but must be present as a file
* nornir3_groups.yaml     ---> is used to group your hosts an provide a plattform info (used for authentication)
* nornir3_hosts.yaml      ---> is used to put in the hosts you want to run the scripts against in yaml format
    
For more help how to setup a inventory please see ---> https://nornir.readthedocs.io/en/latest/tutorial/inventory.html

When you finisched setting up the inventory you can run the script with ```python3 nornir3_8021x.py```
The scripts start an will ask you for the following input:
    
* Shared Key for the radius server
* Shared Key for TACACS authentication
* ISE API User to login to the ISE API
* ISE API IP to get connected to ISE
* ISE API Password to login to the ISE API
* IP of your first Radius Server
* IP of your second (backup) Radius Server

Username and Password are taken form an enviromental Variable, you can also user getpass here to ask the User to input the credentials.

When you provided this informations the script runs on all the mentioned switches of the ```nornir3_hosts.yaml``` file where dot1x=yes is set ---> `hosts = nr.filter(dot1x="yes")`
The script will set all global commands from the best practice guide for 802.1x of Cisco:
https://www.cisco.com/c/en/us/td/docs/ios-xml/ios/sec_usr_8021x/configuration/xe-3se/3850/sec-user-8021x-xe-3se-3850-book/config-ieee-802x-pba.html#GUID-430BBBAE-CB5D-46F9-80B2-6DF8A5497922

When the script has configured all switches it will add the Switch to the ISE into the appropriated Groups.
In this use case, the groups are derived from the hostname. The location, building and switch type are already stored there. Therefore, the information is only extracted from the hostname and the necessary information for the JSON is generated from it.

If you want to know how to exactly interact with the ERS API of Cisco ISE see here:
https://developer.cisco.com/docs/identity-services-engine/3.0/#!setting-up/cisco-ise