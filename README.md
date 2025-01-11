# Basic_Firewall_Using_Iptables
## Introduction
This project demonstrates how to set up a firewall using **iptables** on a Linux system. Firewalls are critical for network security, helping to control incoming and outgoing traffic based on predefined rules. This project provides a robust and configurable script to implement secure firewall rules.

## Understanding iptables
Iptables operates on the concept of **tables, chains, and rules:**
- **Tables:** Define what kind of operations will be performed. Common tables are:
  - **filter:** Default table for packet filtering.
  - **nat:** Used for network address translation.
  - **mangle:** Alters packet headers.
  - **raw:** Used for exemptions from connection tracking.
  
- **Chains:** Traffic flows through these predefined chains:
  - **INPUT:** For incoming traffic.
  - **OUTPUT:** For outgoing traffic.
  - **FORWARD:** For traffic routed through the machine.

- **Rules:** Define actions for packets. Common actions include:
  - **ACCEPT:** Allow the packet.
  - **DROP:** Silently discard the packet.
  - **REJECT:** Discard the packet and send an error message back.


## Objectives
1. Protect the system from unauthorized access.
2. Allow necessary services like SSH, HTTP, and HTTPS.
3. Log and monitor dropped packets for security analysis.
4. Ensure persistent firewall rules across reboots.


## Setup and Requirements
**Prerequisites:**
1. A Linux system with `iptables` installed.
2. Root or sudo privileges to execute firewall commands.
3. Basic knowledge of Linux and networking.


**Tools Used:**
- **iptables** for configuring the firewall.
- **iptables-save** for saving rules.


**Tags used:**

-X : This option deletes (removes) user-defined chains in the current table.

-F : Stands for flush. It deletes all rules from the specified chain(s) or all chains in the active table if no specific chain is mentioned.

-t : Specify tables. (NAT)

-P : Sets the default policy for a specific chain. A default policy determines what happens to packets that do not match any rule in the chain.

-A : Appends (adds) a new rule to the specified chain. (INPUT, FORWARD, OUTPUT)

-j : Specifies the action to take if the packet matches the rule. In this case, it accepts the packet. (ACCEPT, DROP, REJECT)

-I : Specifies the input interface.

-o : Specifies the output interface.

-m : This specifies the use of the module (conntrack)

--ctstate : Specifies the connection states for which the rule applies (ESTABLISHED, RELATED)

-p : Specifies the protocol to filter. (tcp, udp)

--dport : Specifies the destination port.



**1. Flushing Existing Rules**

Clears all existing rules and chains to start with a clean configuration.
```
sudo iptables -F
sudo iptables -X
sudo iptables -t nat -F
sudo iptables -t nat -X
```

![image](https://github.com/user-attachments/assets/a3a40c6d-eca4-4aec-ae31-22b1262a4b31)


**2. Setting Default Policies**

Drops all incoming and forwarded traffic by default, allowing only outgoing traffic.
```
sudo iptables -P INPUT DROP
sudo iptables -P FORWARD DROP
sudo iptables -P OUTPUT ACCEPT
```

![image](https://github.com/user-attachments/assets/6b0ba6d1-4600-4963-b359-0b28443455d6)


**3. Allowing Loopback Traffic**

Allows communication within the system via the loopback interface.

```
sudo iptables -A INPUT -i lo -j ACCEPT
sudo iptables -A OUTPUT -o lo -j ACCEPT
```

![image](https://github.com/user-attachments/assets/e9c9b270-29ce-460b-866a-d1c6140ef7a3)


**4. Allowing Established and Related Connections**

Permits responses to outgoing requests and traffic related to established connections.

```
sudo iptables -A INPUT -j ACCEPT -m conntrack --ctstate ESTABLISHED,RELATED
sudo iptables -A OUTPUT -j ACCEPT -m conntrack --ctstate ESTABLISHED,RELATED
```

![image](https://github.com/user-attachments/assets/b23fd395-a0c4-43fe-a202-8834180c4deb)


**5. Deleting Rules**

Delete rules

```
sudo iptables -D INPUT 2
sudo iptables -D OUTPUT 2
```

![image](https://github.com/user-attachments/assets/fc384b14-8ff6-49f7-9e9f-fdee098c5354)


**6. Adding Comments**

Comments make your rule sets more readable. It is used to add comments.

```
sudo iptables -A INPUT -j ACCEPT -m conntrack --ctstate ESTABLISHED,RELATED -m comment --comment 'Connection state'
sudo iptables -A OUTPUT -j ACCEPT -m conntrack --ctstate ESTABLISHED,RELATED -m comment --comment 'Connection state'
```

![image](https://github.com/user-attachments/assets/c303d937-96d0-4222-ab81-f60aedfec0d2)

**7. Allowing Specific Services**

```
sudo iptables -A INPUT -j ACCEPT -p icmp --icmp-type 8
sudo iptables -A INPUT -j ACCEPT -p tcp --dport 22
sudo iptables -A OUTPUT -j ACCEPT -p icmp --icmp-type 8
sudo iptables -A OUTPUT -j ACCEPT -p tcp --dport 22
sudo iptables -A OUTPUT -j ACCEPT -p tcp --dport 80
sudo iptables -A OUTPUT -j ACCEPT -p tcp --dport 443
sudo iptables -A OUTPUT -j ACCEPT -p tcp --dport 53
sudo iptables -A OUTPUT -j ACCEPT -p udp --dport 53
sudo iptables -A OUTPUT -j ACCEPT -p udp --dport 123
```

![image](https://github.com/user-attachments/assets/fed61e6f-3076-4f48-9fe4-d5afdd7c72fc)


**8. Saving Rules Persistently**

It saves the rules
```
sudo sh -c “iptables-save > /etc/iptables/rules.v4”
sudo sh -c “ip6tables-save > /etc/iptables/rules.v6”
```

## Testing the Firewall

1. Verify active rules:

```
sudo iptables -L -n -v
```

![image](https://github.com/user-attachments/assets/7facdc47-305b-48ec-a91d-30ece9717544)


2. Test allowed services (e.g., SSH, HTTP).


Save this script as firewall.sh, make it executable, and run it:

```
chmod +x firewall.sh
sudo ./firewall.sh
```


## Conclusion

This project demonstrates how to create a robust and flexible firewall using `iptables`. By tailoring the rules to your specific needs, you can secure your system effectively against unauthorized access and potential threats.
