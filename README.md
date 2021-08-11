# Red vs Blue

The main purpose of this repository is to showcase a penetration testing report including perspectives of both Red and Blue teams. This presentation has the entire corporation as a potential audience and has been designed with this intent.

The presentation contains the following contents:
- Network Topology
- Red Team: Security Assessment
- Blue Team: Log Analysis and Attack Characterization
- Hardening: Proposed Alarms and Mitigation Strategies

## Network Topology

The network used in this project is made up of three machines titled Kali, Capstone, and ELK:

Network Address Range:192.168.1.0/24
Netmask: 255.255.255.0

Machines
IPv4: 192.168.1.90
OS: Linux
Hostname: Kali

IPv4: 192.168.1.105
OS: Linux
Hostname: Capstone

IPv4: 192.168.1.100
OS: Linux
Hostname: ELK

## Red Team: Security Assessment 

The red team used a variety of tools in their assessment including nmap, hydra, etc.

- Nmap provided the IP addresses of the Capstone and ELK machines on the target network, it also allowed assumptions to be made about their roles.
- General browsing on the http site found at the Capstone VM's IP of 192.168.1.105 reveals 'secret' folder at 192.168.1.105/company_folders/secret_folder/
- Authentication hint of username: "ashton" paired with hydra brute force attack provided password: "leopoldo".
- Locating instructions to access webdav directory and decoding hash provides full access to webdav directory.
- Uploading PHP reverse shell created in msfvenom to allow backdoor connection from outside network.

## Blue Team: Log Analysis and Attack Characterization

- The port scan occurred at 17:43, Connections over time shows that the port scan is scanning the most likely 1000+ ports.
- Under top HTTP requests we can see that there was 15,722 requests made to the "secret_folder" directory. This indicates a brute force attack, specifically on content which is known to be sensitive.
- Kibana search {user_agent.original : "Mozilla/4.0 (Hydra)"} shows 15,722 hits with the original user agent being “Mozilla/4.0 (Hydra)”
- Under top HTTP requests we also see 56 requests to the webdav directory. We can also see 20 requests to a suspicious webdav/shell.php path. This file is most likely a malicious upload achieved by attackers gaining access to the secret folder through brute force.

## Hardening: Proposed Alarms and Mitigation Strategies

| Alarms                                          | System Hardening                                                                             |
| ------------------------------------------------| -------------------------------------------------------------------------------------------- |
| Total connection requests exceeds 1000/10min   | Local port scans, closing ports, update software & firewall, set firewall to block port scans|
| 'secret_folder' requests exceeds 10 (notify SOC)| Lockout procedure, stronger password policy, best course of action removing 'secret_folder'  |
| All webdav uploads notify SOC and dev teams     | If no uploads wanted system should be configured to read only, blocking all uploads to webdav|
