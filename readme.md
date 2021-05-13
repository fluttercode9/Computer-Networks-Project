# Task 1:

The organization plans to start operations in 3 buildings, each of them provides up to 1000 IP devices.

1. Design and document a solution prototype using the software  CISCO Packet Tracer.

# Scheme

![task 1](stage-01.svg)

# Solution:

 * Adresy poszczególnych sieci IP
 * Adresację linków pomiędzy routerami
 * Tablice routingów na poszczególnych routerach

I used public IP addresses from the [website](https://42.pl/pl/networks.html?html=1) to address individual networks. Each network is able to handle 1022 usable ip addresses.


### Networks

 ##### Network 1 : 
 
* ip:91.236.84.0/22
* mask:255.255.252.0
* min:91.236.84.1
* max:91.236.87.254
* hosts_max:1022

###### Devices:
Server - 91.236.84.2
Router - 91.236.84.1

 ##### Network 2 : 
 
* 185.33.36.0/22
* mask:255.255.252.0
* min:194.28.228.1
* max:194.28.231.254
* max_hosts:1022

###### Devices:
* Laptop - 185.33.36.2
* Router - 185.33.36.1


 ##### Network 3 : 
 
* 194.28.228.0/22
* mask:255.255.252.0
* min:194.28.228.1
* max:194.28.231.254
* max_hosts:1022

###### Devices:
Laptop - 194.28.228.2 \
Router - 194.28.228.1 

## Link addressing between routers

The networks are linked by 3 routers. each router-router connection has a private network(10.x.x.x). For each router I added 2 new Fiber interfaces able to handle these connections.

##### Network1 - Network3: 10.0.0.8/30
Router1 ip_2: 10.0.0.9 ------- Router3 ip_2:10.0.0.10

##### Network1 - Network2: 10.0.0.0/30
Router1 ip_1:10.0.0.1  ------- Router2 ip_1:10.0.0.2

##### Network2 - Network3: 10.0.0.4/30
Router2 ip_2:10.0.0.5  ------- Router2 ip_1:10.0.0.6


## Routing Tables

### Router1:
| Destination                    | Gateway               
| -------------                 |:-------------:            
| 185.33.36.0/22|  | 10.0.0.2         |
| 194.28.228.0/22|  | 10.0.0.10         |


dest--------------------gw  
185.33.36.0/22  - 10.0.0.2  
194.28.228.0/22 - 10.0.0.10 

#### Router2:
| Destination                    | Gateway               
| -------------                 |:-------------:            
| 91.236.84.0/22|  | 10.0.0.1          |
| 194.28.228.0/22|  | 10.0.0.6         |
dest--------------------gw  
91.236.84.0/22   - 10.0.0.1  
194.28.228.0/22 - 10.0.0.6 

#### Router3:
| Destination                    | Gateway               
| -------------                 |:-------------:            
| 91.236.84.0/22|  | 10.0.0.9         |
| 185.33.36.0/2|  |  10.0.0.5        |
dest--------------------gw   
91.236.84.0/22   - 10.0.0.9  
185.33.36.0/22 - 10.0.0.5  


