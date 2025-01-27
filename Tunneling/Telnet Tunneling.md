# Telnet Tunneling

Telnet tunneling is a method of encapsulating Telnet traffic within another protocol, usually to enhance security or to bypass firewalls. Telnet itself is an unencrypted protocol that allows users to connect to remote devices. Because it transmits data in plain text, it can be vulnerable to eavesdropping and other security threats.

### Scope:
The objective of this exercise is to review a provided PCAP file to identify potential Telnet tunneling activities. This assessment will involve examining network traffic for signs of Telnet use and tunneling behavior, with a focus on detecting unauthorized or suspicious activities that could indicate the presence of a tunnel. 

### Tools and Technologies:
Linux, Wireshark, and IPtables

## Example 1

1. For this first example/scenario, I will be looking at a pcap where there is a high amount of TCP PSH flags sent to a host in the network:

![Pasted image 20240408134727](https://github.com/lm3nitro/Projects/assets/55665256/d1b27475-19a2-4462-91ed-06838fdc7d08)

In the TCP protocol, the PSH (Push) flag is used to indicate that the data being sent should be immediately pushed to the receiving application rather than being buffered for later delivery. To detect and prevent the misuse of the PSH flag, it is important to monitor and analyze the TCP traffic and flags in the network. For example, to capture and display only the TCP packets with the PSH flag set, using tcpdump, I used the following command:

```
tcpdump -i eth0 'tcp[13] & 8 != 0' -nntttvvw Abnormal_TCP_PSH.pcap
```

![Pasted image 20240408130203](https://github.com/lm3nitro/Projects/assets/55665256/2216d114-faaf-4a99-9a3f-22f6378989f9)

Here I can see a large amount of PSH flags being sent over port 23 (Telnet). 

I can also see the amount of bytes transfered between these two hosts:

![Pasted image 20240408134957](https://github.com/lm3nitro/Projects/assets/55665256/cea078e3-9d84-4716-9468-2f73a9edfae6)

Attackers can use the PSH flag to transmit malicious data to the receiving end without detection. Taking a cloaser look at the network traffic, I can see that 192.168.10.5 is the one initiating the communication to 192.168.10.7 on port 23.

![Pasted image 20240408112524](https://github.com/lm3nitro/Projects/assets/55665256/7e73f6e1-13f7-495b-86f9-8e39e6456732)

I also noticed that the host was using Telnet protocol to transfer data:

![Pasted image 20240408115444](https://github.com/lm3nitro/Projects/assets/55665256/0b10bb7b-29d2-4aef-82c4-0465b42a24d0)

> [!IMPORTANT]  
> The PSH flags instruct the operating system to send and receive the data immediately. Without this flag, the operating system might decide to wait before sending or passing the received data to the application because it wants to wait for more data to be sent or received to maximize the utilization of the host and network resources. The PSH flag can serve both legitimate and malicious purposes in network security.
>
> On the positive side, it helps reduce latency and overhead in TCP communication, especially for time-sensitive or interactive applications.
> 
> However, attackers can exploit the PSH flag to bypass security mechanisms or launch denial-of-service attacks. 

### Mitigation

Iptables can be used to detect TCP flags and block them, the following command can be used to block PSH flag:

```
iptables -A INPUT -p tcp --tcp-flags PSH PSH -j DROP
```
To enable logging into iptables, I needed to add a new rule to the iptables configuration. This can be done using the following command:

![Pasted image 20240408151509](https://github.com/lm3nitro/Projects/assets/55665256/df5fa797-e628-4f5e-91de-f69d11218dfa)

To log actions relating to INPUT chain rule execute the following command as root

```
iptables -A INPUT -j LOG 

```

> [!TIP]
> Using option A, it stands for "Append". It tells iptables to add the rule to the specified chain.
> Make sure to use -I instead of -A because using the -I rule inserts the log rule at the beginning of the INPUT chain, meaning it will match before other rules. This can be useful if you want to log traffic before any other rules affect it. 

FORWARD chain:

```
iptables -I FORWARD 1 -j LOG
```

OUTPUT chain:

```
iptables -I OUTPUT 1 -j LOG
```

This command adds a new rule that logs all incoming traffic. To log only specific types of traffic, the **`-p`** option can be used to specify the protocol, such as TCP or UDP, and the `**-s**` option to specify the source IP address.

```
sudo iptables -A INPUT -p TCP -s 192.168.10.0/24 -j LOG 
```

To define the level of LOG generated by iptables use `--log-level` followed by the level number.

```
sudo iptables -A INPUT -s 192.168.10.0/24 -j LOG --log-level 4 
```

In addition, prefixes can be added in generated Logs, this will make it easy to search for specific logs in a huge file.

```
sudo iptables -A INPUT -s 192.168.10.0/24 -j LOG --log-prefix '** ALERT **' 
```


## Example 2

In this next example, I am analyzing a pcap where there is an high amount of traffic but with no associated application layer. Looking at the protocol Hierarchy, there is no application protocol, all I see is mainly TCP related traffic:

![Pasted image 20240408121115](https://github.com/lm3nitro/Projects/assets/55665256/cb0fe3f8-c11b-4a16-abf3-567802556a52)

> [!NOTE]  
> If Wireshark is unable to identify the TCP-based application layer protocol, it will display the protocol as 'TCP' along with Layer 7 (Data) information. This could indicate that someone on the network is using an application with a non-standard port, potentially to avoid detection.
> There are instances when applications can be manually decoded, typically when the user is familiar with the application in use and can specify it within Wireshark.

Here I will be using the manual decode feature in Wireshark in order to decode the traffic:

![Pasted image 20240408144217](https://github.com/lm3nitro/Projects/assets/55665256/e2a433e1-d135-4cef-a33b-7bb1b5fe7631)

Here I am decoding the traffic as Telnet:

![Pasted image 20240408144426](https://github.com/lm3nitro/Projects/assets/55665256/774fea91-6c9b-4c95-9b50-ec6f965e1b09)

Looking at the traffic, Wireshark was able to decode the application:

![Pasted image 20240408144939](https://github.com/lm3nitro/Projects/assets/55665256/097cdc9b-b90c-46df-932b-cf8f84df8044)

There is a high amount of traffic going to a destination server on port 9999. As mentioned above, sometimees attackers use non-standard ports or to avoid detection. 

![Pasted image 20240408115702](https://github.com/lm3nitro/Projects/assets/55665256/05188266-f754-4a9f-b8b8-4d923496cee1)

![Pasted image 20240408122105](https://github.com/lm3nitro/Projects/assets/55665256/9b3bd981-daa4-40cb-bfc7-fcfe63cb61ac)

I see that in the traffic highlighted above it is showing that the Push flag is set. Taking a look at the Push flags used in the pcap, below is the statistical overview:

![Pasted image 20240408160310](https://github.com/lm3nitro/Projects/assets/55665256/fee02444-ba3d-40cd-a88d-b3911a770d86)

Taking a look at the packet length:

![Pasted image 20240408121906](https://github.com/lm3nitro/Projects/assets/55665256/25e527aa-8fbb-4f66-8f39-c82dc45c8749)

> [!NOTE]  
> Another example of an application with no identity in Wireshark is Netcat. Here is an example:
>
> Launching Reverse (Backdoor) Shells: A reverse shell is a remote access approach where you run administrative commands from one terminal while connecting to another server on the network.
>
> To better demontrate, I enabled the shell tool using a Netcat reverse shell command:
>
> ```
> nc -n -v -l -p 9999 -e /bin/bash
> ```
> 
> Then from another host on my network, I tested:
>
> ```
> nc -nv 127.0.0.1 9999
> ```
> Here is the traffic generated in Wireshark:
>
> ![Pasted image 20240408153823](https://github.com/lm3nitro/Projects/assets/55665256/39f27b5f-5969-4592-974c-e3fe8a2f9290)
>
> As seen in the screenshot above, the traffic is only showing TCP and nothing related with Netcat:
> 
> ![Pasted image 20240408153737](https://github.com/lm3nitro/Projects/assets/55665256/c9fd0bae-3839-4e9b-84a5-d5145b502481)

### Summary:

Here I analyzed 2 different pcap files that both suggested Telnet Tunneling activity. In both I saw a observed a high amount of PSH flags being sent. While the PSH flag itself is not something specific to Telnet tunneling, it can be used in Telnet sessions to influence the behavior of TCP connections. Another observation was that in the second pcap, Wireshark was not able to identify the application. This highlights how tools like Wireshark may struggle to recognize certain protocols if they are not standard or are deliberately disguised. 

Through this analysis, I gained insights into the use of PSH flags in Telnet tunneling and how they can influence TCP behavior. Additionally, I learned that Wireshark may not always identify applications when non-standard or obfuscated protocols are in use, emphasizing the need for advanced detection techniques. 
