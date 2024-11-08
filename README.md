# Mimicking firewall and Intrusion Detection System IDS using Access Control Lists ACLs


## Objective

The objective of the firewall and IDS simulation lab is to design and implement a network security setup that simulates basic firewall and intrusion detection system (IDS) functionalities, using access control lists (ACLs) to control traffic between an internal and external network.

By configuring ACLs on a router and using Packet Tracer’s simulation mode, this project aims to:

- Protect the internal network by blocking unauthorised access from the external network.
- Allow legitimate internal network traffic to communicate with external networks while preventing unsolicited inbound connections.
- Simulate intrusion detection by observing and identifying unauthorised access attempts, such as external devices attempting to reach internal resources.
- Test and validate security policies through simulated attacks or access attempts to ensure ACL rules are correctly configured to secure the network.

This project showcases basic network security practices, reinforcing the importance of segmenting networks, controlling access, and monitoring for potential intrusions in a controlled environment. The objective is to provide a practical understanding of network access control and basic IDS principles, which are critical skills for network security and administration.

## Skills Learned

- Access Control Lists (ACL) Configuration.
- Basic Firewall Configuration.
- Network Segmentation.
- Intrusion Detection Simulation.
- IP Addressing and Subnetting.
- Packet Filtering and Traffic Monitoring.
- Testing and Troubleshooting Network Security Policies.
- Understanding of Network Security Concepts.

## Tools Used

- Cisco Packet Tracer (overall environment for designing and testing the network).
- Access Control Lists (ACLs) (simulating firewall functionality).
- Ping and Simulation Mode (for network testing and monitoring unauthorised traffic).
- Routers, Switches, and PCs in Packet Tracer (to build the topology and generate traffic for testing).

## Steps

![Firewall_Topology](https://github.com/user-attachments/assets/5d761371-8806-4d21-a6c1-5fce90e241b4)

### Network Topology Setup

Devices Needed:
- One Router.
- One Switch.
- Multiple PCs (in this case 2 PCs).

Objective:
- Set up an internal network protected by a simulated firewall using ACLs on the router.
- Configure basic traffic filtering to simulate a firewall.
- Use Packet Tracer’s simulation mode to monitor and detect unauthorised traffic.

### Creating the Topology in Packet Tracer

Create the topology shown in the image above.

### Configure devices

Configure basic settings for the router shown below.

```bash
Router>enable
Router#config t

Enter configuration commands, one per line.  End with CNTL/Z.

#Create hostname for Router as R1.
Router(config)#hostname R1

#Set no DNS lookup for R1.
Router(config)#no ip domain-lookup

#Set Privilaged Exec mode as the following.
R1(config)#enable secret $Cisc0!!PRIV*

#Set User Exec mode as the following with a timeout of 5 mins.
R1(config)#line console 0
R1(config-line)#password $Cisc0!!PRIV*
R1(config-line)#login
R1(config-line)#exec-timeout 5 0
R1(config-line)#exit

#Set VTY lines 0 15 as the following with a timeout of 7 mins with only SSH enabled.
R1(config)#line vty 0 15
R1(config-line)#password $Cisc0!!VTY**
R1(config-line)#login
R1(config-line)#exec-timeout 7 0
R1(config-line)#transport input SSH
R1(config-line)#exit

#Set the login so that the User only has 3 attempts within 2 mins to enter the correct password otherwise it will block the user from accessing the router for 3 mins.
R1(config)#login block-for 180 attempts 3 within 120

#Encrypt all plain-text passwords and set the banner with the appropriate message.
R1(config)#service password-encryption 
R1(config)#banner motd #Authorized Users Only!#
R1(config)#end

#Set clock
R1#clock set 13:00:00 NOV 4 2024


#Save Configuration
R1# copy running-config startup-config
```

### Assign IP Addresses

Router Interface Configurations:

- Configure GigabitEthernet 0/0/0 (internal network) with IP address 192.168.1.1/24.

```bash
R1(config)#interface GigabitEthernet 0/0/0
R1(config-if)#description Connects to LAN
R1(config-if)#ip address 192.168.1.1 255.255.255.0
R1(config-if)#no shutdown
R1(config-if)#exit
```

- Configure GigabitEthernet 0/0/1 (external network) with IP address 192.168.2.1/24.

```bash
R1(config)#interface GigabitEthernet 0/0/1
R1(config-if)#description Connects to LAN
R1(config-if)#ip address 192.168.2.1 255.255.255.0
R1(config-if)#no shutdown
R1(config-if)#exit
```

### PC Configurations:

- Assign PC1 an IP address of 192.168.1.10 with a default gateway of 192.168.1.1.

![PC-1 IP Configuration](https://github.com/user-attachments/assets/66f4f855-98e6-4571-a1c3-c06eadc7ccc4)


- Assign PC2 an IP address of 192.168.2.10 with a default gateway of 192.168.2.1.

![PC-2 IP Configuration](https://github.com/user-attachments/assets/05bc5962-0ff9-4120-b66f-9612e918991f)


Configuring ACLs for Basic Firewall Functionality

ACLs can be applied to router interfaces to block or allow specific traffic, simulating a basic firewall.

In this case we will only allow traffic into the network if an only if it is to http or https.


Go to the router’s command line interface (CLI) and apply the following commands to configure ACLs:

```bash
R1> enable 

# Creating an ACL to block external access to the internal network 
R1# configure terminal
R1(config)# 
R1(config)# access-list 101 permit tcp any any eq 80
R1(config)# access-list 101 permit tcp any any eq 443
R1(config)# access-list 101 deny ip any any

# Apply the ACL to the external-facing interface 
R1(config)# interface GigabitEthernet 0/1 
R1(config-if)# ip access-group 100 in 
R1(config-if)# exit
```

### Testing the Firewall and IDS 

Packet Tracer does not support a full IDS, but you can monitor for “intrusions” by:

- Using Packet Tracer’s simulation mode to observe traffic.
- Generating unauthorised traffic (e.g., a ping from PC2 to PC1) and checking if it gets blocked by the ACL.
- This can act as a basic simulation for detecting unwanted or unauthorised access attempts.

The following is the action of accessing the local network in simulation mode.

Accessing the web server from PC-2 for HTTP and HTTPS.

[PC-2 to server on HTTP and HTTPS](https://github.com/user-attachments/assets/d73751b0-2d3b-48f9-a9b2-c267993d9818)

Pinging Server from PC-2.

[Ping PC-2 to Server](https://github.com/user-attachments/assets/acc45d2b-c227-49e7-8d11-5131f5f9818a)

Pinging PC-1 from PC-2.

[Ping PC-1 from PC-2](https://github.com/user-attachments/assets/de9ea30f-3cad-470c-83e0-d83c0b2378a6)

Although all of these are rejected because of the aforementioned ACLs pings from PC-1 can communicate outside of the network to PC-2 the reply is not received because of the ACLs as shown below.

[Ping PC-1 from PC-2](https://github.com/user-attachments/assets/f93671d6-6d08-4031-a542-1e92ba7eaf8d)

This is also the same for the server to PC-2 as well.

[Ping from Server to PC-2](https://github.com/user-attachments/assets/402c4e0a-9d98-4500-9238-1fcef35b8dea)


## Conclusion

This project demonstrates a basic simulation of firewall and IDS functionalities using ACLs and Packet Tracer's simulation mode. The firewall ACL rules effectively block unauthorized access from the external network while allowing internal users to initiate outbound connections. The simulation mode provides insights into blocked traffic, simulating an IDS that detects unauthorized access attempts.

