# ~~~~~~~~~~IN PROGRESS~~~~~~~~~~~~
# HomeLab_Virtual_Network


#### In this project im building my own Virtual network which will consist of 3 zones: Work-Zone,Security-Zone, and DMZ-Zone . My Work-Zone subnet which will include My Active Directory Domain and a couple of Windows end-users. Another subnet will contain my SIEM, which is a Linux machine running Splunk. The last subnet will be a DMZ-zone that will contain a linux machine running a Web-server. The WAN interface will sit on a private network to not expose my environment/Host to the public Wide Area Network (but it will mimic a WAN-network). 
#### Setup is being virtualized inside Virtualbox, using a hyperviser type 2.
-----------------


### Network Topology
- The network is comprised of Following:
1) Pfsense Router/Firewall
2) Active Directoroy-DC
3) Two End-users
4) SIEM (Splunk)
5) Web-Server 

![alt text](https://github.com/pg-cy/Virtual_Network_Configuration/blob/master/images/Network_Topology.drawio.png)


### PfSense interface setup
- In this situation i have my WAN interface access coming from a private Network address. I wanted to keep everything Virtual so the WAN subnet will act as a public network.
- Interface configuration can be completed during setup or at the start-up menu.

![alt text](https://github.com/pg-cy/Virtual_Network_Configuration/blob/master/images/FW_Interfaces.png)



|#|Pfsense-Interface|Zone-Type| Subnet/CIDR |Netmask|
|---|--------------|------|----|-----|
|1|WAN|Public Network               |    |   |   |
|2|LAN|WORK-Zone|172.16.0.0/16|255.255.0.0|
|3|OPT1|Security-Zone|10.0.55.0/24|255.255.255.0|
|4|OP2|DMZ-Zone|10.10.10.0/24|255.255.255.0|


|Host-Function   |     Hostname  |     IP   	      | OS|	    zone|         
-----------------|------------|-------------|------|--------|
|Domain Controler|Gandalf-DC |172.16.0.14   | Windows 10   | Work-Zone|
|End-User        |Legolas    |172.16.0.15   | Windows 10	| Word-Zone|
|End-User        |Gimli	    |172.16.0.16   | Window 10    |	Word-Zone|
|SIEM-Splunk            |	      |10.0.55.100    |Ubuntu-Linux|Security-Zone	|	  
|Pfsense-Router (Default Gate-way) / Firewall |FW01|(10.0.2.15) (172.16.0.1) (10.10.10.10) (10.0.55.1) | FreeBSD 	|
|Web-Server|        webVM|   10.10.10.10   |Ubuntu-Linux| DMZ-Zone|


### Routing (Allowed/Blocked)
- Network is segmented for increased security.
- WAN is mimicking our Public Network. The only thing were exposing to the Public Network will be our web-server. WAN will not have acess to our work-zone or security-zone 
- DMZ-zone which is where our webserver lies, is Completely cut off from accessing our internal network; WORK-zone or Security zone. Only Traffic allowed to pass Locally From DMZ out will be the logs to our SIEM.
- The Work-zone can connect with others on the internal network, and reach out to our web-server, but will not be allowed to connect to our SIEM unless its Log traffic
- Security-Zone will aggragate logs from all machines on our internal network
------------------
### ---WAN Rules---
![alt text](https://github.com/pg-cy/Virtual_Network_Configuration/blob/master/images/WAN_rule.png)
### ---Work-zone Rules---
![alt text](https://github.com/pg-cy/Virtual_Network_Configuration/blob/master/images/LAN_rules.png)
### ---security-zone Rules---
![alt text](https://github.com/pg-cy/Virtual_Network_Configuration/blob/master/images/OPT1_rules.png)
### ---DMZ-zone Rules---
![alt text](https://github.com/pg-cy/Virtual_Network_Configuration/blob/master/images/OPT2_rules.png)
-------------------

## Resolving issues
- Throughout this project ive encountered many issues while configuring machines and routing. Ill list all the fixes i found have worked in order to resolve these issues.

1) Check if you Virtual Machines have different MAC-address. They should have their own unique address. You can edit this within virtual box host settings
2) Check if each machine has an IP address, make sure Pfsense DHCP service is running.
3) Check your host routes. Make sure the default gateway your connecting to can reach those networks. If your setup is similiar to mine and you have your WAN sitting on a private address, you can edit your internal host routes using the "ip" command to add more routes to your table.
5) Check the firewall rules on Pfsense, make sure the subnet is allowed to route to the destination.
4) Check to see if you can ping the default gateway, if you can then check if you can ping a host on another network. If you can ping the Pfsense's IP on another subnet, but cant ping any machines on that subnet then it might be a firewall issue.
5) Check to see if the firewall on both hosts is prevent from sending or recieving ICMP requests.
6) If all else fails then you can restart all machines. For the Pfsense machine you can reboot the machine at the startup menu, and after reboot reassign the interfaces.




-----
-Make you AD-DC server a static IP, so the machines know exactly where DNS server is.
--When starting up your lab again, Always start with your Firewall first--then AD-DC---then end-users

Things needed to be fixed, Routing on WAN machines--SInce the pfsense is not my default gateway--wont know how to route to the other net
Ping issues on Windows---Might need to createa rule for inbound--so you can ping that windows machine

--routing issues
-if you can ping other machines on other networks but you cant ping www.google.ca could be a DNS issue
-if you cant ping other machines on another netowkr then its a routing issue, might be a firewall(network or host) blocking or yuor routes are not set up on localhost to reach that network (defualt gateway might not be able to reach a specific network so you need to add another route)

-also add an exception on a host-firewall to be able to ping, inbound---host based firewall can also block ping

