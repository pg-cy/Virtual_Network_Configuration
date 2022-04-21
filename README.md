# ~~~~~~~~~~IN PROGRESS~~~~~~~~~~~~
# HomeLab_Virtual_Network
In this project im building my own Virtual network which will consist of 3 zones: Work-Zone,Security-Zone, and DMZ-Zone . My Work-Zone subnet which will include My Active Directory Domain and a couple of Windows end-users. Another subnet will contain my SIEM, which is a Linux machine running Splunk. The last subnet will be a DMZ zone that will contain a linux machine running a Web-server.  
Setup is being virtualized inside Virtualbox, using a hyperviser type 2.



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

[alt text](https://github.com/pg-cy/Virtual_Network_Configuration/blob/master/images/FW_Interfaces.png)



|#|Zone-Type| Subnet/CIDR |Netmask|
|-----------------|------|----|-----|
|1|WORK-Zone|172.16.0.0/16|255.255.0.0|
|2|Security-Zone|10.0.55.0/24|255.255.255.0|
|3|DMZ-Zone|10.10.10.0/24|255.255.255.0|


|Function   |     HOSTNAME  |     IP   	      | OS|	    zone|         
-----------------|------------|-------------|------|--------|
|Domain Controler|Gandalf-DC |172.16.0.14   | Windows 10   | Work-Zone|
|End-User        |Legolas    |172.16.0.15   | Windows 10	|
|End-User        |Gimli	    |172.16.0.16   | Window 10    |	
|SIEM-Splunk            |	      |10.10.10.100    |Ubuntu-Linux|		  
|Pfsense-Router (Default Gate-way) / Firewall |FW01|(10.0.2.15) (172.16.0.1) (10.10.10.10) (10.0.55.1) | FreeBSD 	|
|Web-Server|        webVM|   10.10.10.10   |
||
||

- Network is segmented to allow for increased security. Completely cutting off DMZ from accessing internal WORK-zone or Security zone.

### Resolving issues
- Throughout this project ive encountered many issues with configuring machines and routing. Ill list all the fixes i found have worked in order to resolve these issues.



-----
-Make you AD-DC server a static IP, so the machines know exactly where DNS server is.
--When starting up your lab again, Always start with your Firewall first--then AD-DC---then end-users

Things needed to be fixed, Routing on WAN machines--SInce the pfsense is not my default gateway--wont know how to route to the other net
Ping issues on Windows---Might need to createa rule for inbound--so you can ping that windows machine

--routing issues
-if you can ping other machines on other networks but you cant ping www.google.ca could be a DNS issue
-if you cant ping other machines on another netowkr then its a routing issue, might be a firewall(network or host) blocking or yuor routes are not set up on localhost to reach that network (defualt gateway might not be able to reach a specific network so you need to add another route)

-also add an exception on a host-firewall to be able to ping, inbound---host based firewall can also block ping

